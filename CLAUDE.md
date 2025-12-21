# CLAUDE.md - RealCoach.ai Development Instructions

This file provides specific guidance for Claude Code when working on the RealCoach.ai project.

## Project Overview

**RealCoach.ai** is a chat-first AI coaching platform with lightweight CRM for solo real estate agents. The core innovation is replacing traditional form-based CRM data entry with natural conversation that automatically maintains database state while providing contextual coaching.

### Core Architecture
- **Frontend**: Next.js 14 with App Router, TypeScript, Tailwind CSS
- **Backend**: Supabase (PostgreSQL + Auth + Storage + Edge Functions)
- **AI**: OpenAI GPT-4 with function calling for database operations
- **Payments**: Stripe subscriptions
- **Deployment**: Vercel with cron jobs

## Key Development Principles

### 1. Chat-First Design
- All primary interactions happen through the chat interface
- Traditional CRM views are read-mostly with strong nudges back to chat
- AI writes to the database, humans read from it
- Every meaningful action creates an event record

### 2. Event-Driven Architecture
```
User Input → AI Processing → Function Calls → Database Events → UI Updates
```
- All CRM changes flow through the events table
- Events create an audit trail for AI actions
- UI components react to event changes
- Events enable coaching insights and pattern recognition

### 3. Progressive Trust Building
- Start with low-impact AI actions
- Show clear audit trails of what AI did
- Provide rollback capabilities for AI actions
- Allow manual override for all operations

## File Structure

```
src/
├── app/                     # Next.js App Router
│   ├── (auth)/             # Authentication routes
│   │   ├── signin/
│   │   └── signup/
│   ├── (dashboard)/        # Main authenticated app
│   │   ├── chat/           # Primary chat interface
│   │   ├── contacts/       # Contact management
│   │   ├── deals/          # Deal pipeline
│   │   ├── timeline/       # Events timeline
│   │   └── settings/       # User settings
│   ├── api/                # API routes
│   │   ├── auth/           # Authentication endpoints
│   │   ├── chat/           # Chat and AI processing
│   │   ├── contacts/       # Contact CRUD
│   │   ├── deals/          # Deal CRUD
│   │   ├── events/         # Event management
│   │   ├── files/          # File upload/processing
│   │   ├── coaching/       # Summary and insights
│   │   └── webhooks/       # Stripe/Supabase webhooks
│   └── globals.css
├── components/
│   ├── ui/                 # Shadcn/ui components
│   ├── chat/               # Chat interface components
│   │   ├── ChatWindow.tsx
│   │   ├── MessageList.tsx
│   │   ├── MessageInput.tsx
│   │   └── ActionConfirmation.tsx
│   ├── crm/                # CRM view components
│   │   ├── ContactList.tsx
│   │   ├── DealPipeline.tsx
│   │   ├── Timeline.tsx
│   │   └── InsightCards.tsx
│   └── common/             # Shared components
│       ├── Layout.tsx
│       ├── Navigation.tsx
│       └── ErrorBoundary.tsx
├── lib/
│   ├── ai/                 # AI orchestration
│   │   ├── openai.ts       # OpenAI client setup
│   │   ├── function-calling.ts # Function schemas
│   │   ├── intent-classifier.ts # Intent detection
│   │   ├── context-manager.ts # Conversation context
│   │   └── validation.ts   # AI action validation
│   ├── database/           # Database operations
│   │   ├── supabase.ts     # Supabase client
│   │   ├── contacts.ts     # Contact operations
│   │   ├── deals.ts        # Deal operations
│   │   ├── events.ts       # Event operations
│   │   └── users.ts        # User operations
│   ├── stripe/             # Payment integration
│   │   ├── client.ts       # Stripe client
│   │   ├── subscriptions.ts # Subscription management
│   │   └── webhooks.ts     # Webhook handlers
│   └── utils/              # General utilities
│       ├── validation.ts
│       ├── formatting.ts
│       └── constants.ts
├── types/                  # TypeScript definitions
│   ├── database.ts         # Database types
│   ├── ai.ts              # AI-related types
│   ├── chat.ts            # Chat types
│   └── api.ts             # API types
└── hooks/                 # React hooks
    ├── useAuth.ts
    ├── useChat.ts
    ├── useContacts.ts
    └── useRealtime.ts
```

## Development Workflow

### 1. Database-First Development
Always start with database schema changes:
```bash
# Create new migration
supabase migration new add_feature_table

# Apply migrations
supabase db push

# Generate types
supabase gen types typescript --local > types/database.ts
```

### 2. AI Function Development Pattern
1. Define function schema in `lib/ai/function-calling.ts`
2. Implement database operation in appropriate `lib/database/` file
3. Add validation in `lib/ai/validation.ts`
4. Test function calling via chat interface
5. Add UI feedback for the action

### 3. Component Development
- Use Shadcn/ui components for consistency
- Follow compound component patterns
- Implement optimistic UI updates
- Add proper loading and error states

## Key Technical Patterns

### 1. AI Function Calling
```typescript
// Function schema definition
export const CREATE_CONTACT_SCHEMA = {
  name: 'create_contact',
  description: 'Create a new contact in the CRM',
  parameters: {
    type: 'object',
    properties: {
      first_name: { type: 'string' },
      last_name: { type: 'string' },
      email: { type: 'string', format: 'email' },
      phone: { type: 'string' },
      contact_type: { type: 'string', enum: ['lead', 'client', 'referral'] }
    },
    required: ['first_name']
  }
};

// Function implementation
export const createContact = async (params: CreateContactParams) => {
  // 1. Validate input
  const validated = await validateContactData(params);

  // 2. Create contact
  const contact = await supabase.from('contacts').insert(validated);

  // 3. Create event
  await createEvent({
    contact_id: contact.id,
    event_type: 'contact_created',
    event_data: { source: 'ai', original_input: params }
  });

  return contact;
};
```

### 2. Event Creation Pattern
```typescript
export const createEvent = async (eventData: CreateEventParams) => {
  const { data, error } = await supabase
    .from('events')
    .insert({
      user_id: getCurrentUserId(),
      ...eventData,
      created_at: new Date().toISOString()
    })
    .select()
    .single();

  if (error) throw new Error(`Failed to create event: ${error.message}`);

  // Trigger UI updates via real-time subscription
  return data;
};
```

### 3. Chat Message Processing
```typescript
export const processChatMessage = async (message: string, context: ConversationContext) => {
  // 1. Save user message
  await saveChatMessage({ content: message, type: 'user' });

  // 2. Get AI response with function calling
  const response = await openai.chat.completions.create({
    model: 'gpt-4',
    messages: buildMessageHistory(context, message),
    functions: AI_FUNCTION_SCHEMAS,
    function_call: 'auto'
  });

  // 3. Execute any function calls
  const actions = [];
  if (response.choices[0].message.function_call) {
    const result = await executeAIFunction(response.choices[0].message.function_call);
    actions.push(result);
  }

  // 4. Save AI response
  await saveChatMessage({
    content: response.choices[0].message.content,
    type: 'assistant',
    actions
  });

  return { content: response.choices[0].message.content, actions };
};
```

### 4. Real-time UI Updates
```typescript
export const useRealtimeEvents = (userId: string) => {
  const [events, setEvents] = useState<Event[]>([]);

  useEffect(() => {
    const subscription = supabase
      .channel('events')
      .on('postgres_changes',
        { event: 'INSERT', schema: 'public', table: 'events', filter: `user_id=eq.${userId}` },
        (payload) => {
          setEvents(prev => [payload.new as Event, ...prev]);
        }
      )
      .subscribe();

    return () => subscription.unsubscribe();
  }, [userId]);

  return events;
};
```

## Development Commands

### Setup Commands
```bash
# Install dependencies
npm install

# Set up Supabase
supabase init
supabase start
supabase db reset

# Development server
npm run dev
```

### Database Commands
```bash
# Create migration
supabase migration new migration_name

# Apply migrations
supabase db push

# Generate TypeScript types
supabase gen types typescript --local > types/database.ts

# Reset database
supabase db reset
```

### Testing Commands
```bash
# Run tests
npm test

# Type checking
npm run type-check

# Linting
npm run lint

# Build
npm run build
```

## Environment Variables

Required environment variables:
```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# OpenAI
OPENAI_API_KEY=your_openai_api_key

# Stripe
STRIPE_SECRET_KEY=your_stripe_secret_key
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=your_stripe_publishable_key
STRIPE_WEBHOOK_SECRET=your_webhook_secret

# App
NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=your_app_url
```

## Quality Standards

### Code Quality
- Use TypeScript strict mode
- Implement proper error boundaries
- Add comprehensive error handling
- Follow ESLint configuration
- Use Prettier for formatting

### Testing Strategy
- Unit tests for utility functions
- Integration tests for API endpoints
- End-to-end tests for critical user flows
- AI function testing with mock responses

### Performance Requirements
- Chat response time: <2 seconds
- Page load time: <1 second
- Database query optimization
- Image optimization and lazy loading

## Security Considerations

### Data Protection
- Row Level Security (RLS) on all tables
- Input validation on all endpoints
- SQL injection prevention
- XSS protection

### AI Security
- Validate all AI function calls
- Sanitize AI-generated content
- Rate limiting on AI endpoints
- Cost monitoring for API usage

## Deployment Guidelines

### Pre-deployment Checklist
- [ ] All tests passing
- [ ] Environment variables configured
- [ ] Database migrations applied
- [ ] Stripe webhooks configured
- [ ] Error monitoring set up

### Production Deployment
```bash
# Deploy to Vercel
vercel --prod

# Run database migrations
supabase db push --linked

# Monitor deployment
vercel logs
```

## Troubleshooting

### Common Issues
1. **Supabase Connection**: Check URL and API keys
2. **AI Function Errors**: Validate function schemas match implementation
3. **Real-time Updates**: Verify RLS policies allow subscriptions
4. **File Upload Issues**: Check Supabase Storage bucket permissions

### Debug Tools
- Supabase Dashboard for database inspection
- OpenAI Playground for AI testing
- Vercel logs for production debugging
- Browser DevTools for frontend issues

---

**Note**: This file should be updated as the project evolves. Always refer to `REALCOACH_DEVELOPMENT_PLAN.md` for the complete technical specification and project timeline.