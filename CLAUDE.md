# CLAUDE.md - RealCoach.ai Development Instructions

This file provides specific guidance for Claude Code when working on the RealCoach.ai project.

## Current Project Status

**⚠️ IMPORTANT: This project is currently in the PLANNING PHASE**

The repository contains comprehensive planning documentation but **no implementation code yet**. The Next.js application has not been initialized. When working on this project:

1. **First understand the vision** by reading the planning documents
2. **Consult the user** before initializing the Next.js project or making structural changes
3. **Reference the architecture sections below** as guidance for future implementation
4. **Follow the development plan** outlined in `REALCOACH_DEVELOPMENT_PLAN.md`

## Project Overview

**RealCoach.ai** is a chat-first AI coaching platform with lightweight CRM for solo real estate agents. The core innovation is replacing traditional form-based CRM data entry with natural conversation that automatically maintains database state while providing contextual coaching.

### Planned Technology Stack
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

## Current Repository Structure

**What exists NOW:**
```
realcoach-ai/
├── .git/                           # Git repository
├── .gitignore                      # Git ignore configuration
├── CLAUDE.md                       # This file - AI assistant guide
├── README.md                       # Project overview and quick start
├── REALCOACH_DEVELOPMENT_PLAN.md   # Complete technical specification (20KB)
├── docs/
│   ├── api-design.md              # API endpoints and schemas
│   └── development-checklist.md   # Phase-by-phase checklist
└── package.json                    # Dependencies defined (no node_modules yet)
```

**What will be created during Phase 1 (Weeks 1-2):**
```
realcoach-ai/
├── src/                           # Source code (to be created)
│   ├── app/                       # Next.js App Router
│   ├── components/                # React components
│   ├── lib/                       # Utility functions and integrations
│   ├── types/                     # TypeScript definitions
│   └── hooks/                     # React hooks
├── supabase/                      # Supabase configuration (to be created)
│   ├── migrations/                # Database migrations
│   ├── functions/                 # Edge functions
│   └── config.toml                # Supabase config
├── public/                        # Static assets (to be created)
├── tests/                         # Test files (to be created)
├── next.config.js                 # Next.js configuration (to be created)
├── tailwind.config.js             # Tailwind configuration (to be created)
├── tsconfig.json                  # TypeScript configuration (to be created)
└── .env.local                     # Environment variables (to be created)
```

## Planned Application Structure (Post-Implementation)

When the application is built, it will follow this structure:

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
│   ├── crm/                # CRM view components
│   └── common/             # Shared components
├── lib/
│   ├── ai/                 # AI orchestration
│   ├── database/           # Database operations
│   ├── stripe/             # Payment integration
│   └── utils/              # General utilities
├── types/                  # TypeScript definitions
└── hooks/                  # React hooks
```

## Navigation Guide for AI Assistants

### Key Documentation Files

1. **REALCOACH_DEVELOPMENT_PLAN.md** (20KB) - Read this FIRST
   - Complete technical specification
   - 12-week development timeline with 6 phases
   - Detailed database schema design
   - AI orchestration architecture
   - Risk assessment and mitigation strategies
   - Success metrics and launch strategy

2. **docs/api-design.md** - API Reference
   - All REST endpoint specifications
   - AI function calling schemas
   - Request/response interfaces
   - Error handling patterns

3. **docs/development-checklist.md** - Implementation Tracking
   - Phase-by-phase task breakdown
   - Checklist format for progress tracking
   - Covers all 6 development phases

4. **README.md** - Project Overview
   - High-level project description
   - Quick start guide (for when development begins)
   - Success metrics and timeline

### How to Work with This Repository

**When asked about the project:**
1. Reference `REALCOACH_DEVELOPMENT_PLAN.md` for technical details
2. Explain the current planning phase status
3. Clarify what exists vs. what's planned

**When asked to start development:**
1. Confirm with user before initializing Next.js project
2. Follow Phase 1 checklist in `docs/development-checklist.md`
3. Use database schema from `REALCOACH_DEVELOPMENT_PLAN.md`
4. Reference architecture patterns below

**When asked to add features:**
1. Check which phase the feature belongs to
2. Ensure prerequisites from earlier phases are complete
3. Follow the development patterns outlined below

## Development Workflow (When Implementation Begins)

### 1. Project Initialization (Phase 1, Week 1)
```bash
# Initialize Next.js 14 with TypeScript and Tailwind
npm create-next-app@latest . --typescript --tailwind --eslint --app

# Install core dependencies
npm install @supabase/supabase-js openai stripe @stripe/stripe-js

# Install UI dependencies (Shadcn/ui prerequisites)
npm install tailwindcss lucide-react class-variance-authority clsx tailwind-merge

# Install Radix UI components (for Shadcn/ui)
npm install @radix-ui/react-avatar @radix-ui/react-button @radix-ui/react-dialog
npm install @radix-ui/react-dropdown-menu @radix-ui/react-label @radix-ui/react-select
npm install @radix-ui/react-separator @radix-ui/react-slot @radix-ui/react-tabs
npm install @radix-ui/react-toast

# Install form and validation libraries
npm install zod react-hook-form @hookform/resolvers date-fns react-textarea-autosize

# Initialize Supabase
npm install supabase --save-dev
npx supabase init
npx supabase start

# Create initial directory structure
mkdir -p src/{app,components,lib,types,hooks}
mkdir -p src/components/{ui,chat,crm,common}
mkdir -p src/lib/{ai,database,stripe,utils}
```

### 2. Database-First Development (Phase 1, Week 2)
Once Supabase is initialized:
```bash
# Create new migration
supabase migration new add_feature_table

# Apply migrations
supabase db push

# Generate TypeScript types
supabase gen types typescript --local > src/types/database.ts
```

### 3. AI Function Development Pattern (Phase 2)
When implementing AI features:
1. Define function schema in `src/lib/ai/function-calling.ts`
2. Implement database operation in appropriate `src/lib/database/` file
3. Add validation in `src/lib/ai/validation.ts`
4. Test function calling via chat interface
5. Add UI feedback for the action
6. Create event record for audit trail

### 4. Component Development (All Phases)
- Use Shadcn/ui components for consistency
- Follow compound component patterns
- Implement optimistic UI updates
- Add proper loading and error states
- Keep components chat-aware (provide nudges back to chat for CRM views)

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

### Current State (Planning Phase)
```bash
# Check what's installed
npm list --depth=0

# Verify package.json
cat package.json

# Check git status
git status
```

### When Development Begins (Phase 1+)

#### Setup Commands
```bash
# Install dependencies (after Next.js initialization)
npm install

# Set up Supabase local development
supabase init
supabase start
supabase db reset

# Start development server
npm run dev
```

#### Database Commands
```bash
# Create migration
supabase migration new migration_name

# Apply migrations locally
supabase db push

# Generate TypeScript types
npm run db:generate-types
# Or manually:
# supabase gen types typescript --local > src/types/database.ts

# Reset database (destructive - use carefully)
npm run db:reset
```

#### Testing Commands
```bash
# Run tests
npm test

# Run tests in watch mode
npm run test:watch

# Type checking
npm run type-check

# Linting
npm run lint

# Build for production
npm run build
```

#### Deployment Commands
```bash
# Deploy to Vercel
vercel --prod

# Apply database migrations to production
supabase db push --linked

# Check deployment logs
vercel logs
```

## Environment Variables

### Setup Instructions
When development begins, create `.env.local` in the project root:

```bash
# Supabase (get from Supabase dashboard or supabase status)
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_service_role_key

# OpenAI (get from platform.openai.com)
OPENAI_API_KEY=your_openai_api_key

# Stripe (get from Stripe dashboard - start with test keys)
STRIPE_SECRET_KEY=your_stripe_secret_key
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=your_stripe_publishable_key
STRIPE_WEBHOOK_SECRET=your_webhook_secret

# App Configuration
NEXT_PUBLIC_APP_URL=http://localhost:3000
NODE_ENV=development
```

### Environment Variable Security
- ⚠️ **NEVER commit `.env.local` or `.env.production` to git**
- The `.gitignore` file already excludes these files
- Use Vercel's environment variable settings for production
- Store service role keys securely (they bypass Row Level Security)

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

## Working with Planning Documents

### Before Starting Any Implementation

1. **Review the Development Plan**
   ```bash
   # Read the comprehensive technical specification
   cat REALCOACH_DEVELOPMENT_PLAN.md
   ```
   This 20KB document contains:
   - Complete 12-week timeline
   - Database schema with SQL
   - AI orchestration architecture
   - Risk assessment and mitigation
   - File structure recommendations

2. **Check the API Design**
   ```bash
   # Review all API endpoints and schemas
   cat docs/api-design.md
   ```

3. **Follow the Checklist**
   ```bash
   # Track progress through development phases
   cat docs/development-checklist.md
   ```

### Making Changes to Documentation

When the project evolves:
- Update this CLAUDE.md to reflect new conventions
- Keep REALCOACH_DEVELOPMENT_PLAN.md as the source of truth for architecture
- Update API documentation when endpoints change
- Mark completed items in the development checklist

## Troubleshooting

### Planning Phase Issues
- **No node_modules**: Expected - run `npm install` after Next.js initialization
- **No src/ directory**: Expected - will be created during Phase 1
- **No .env.local**: Expected - create when Supabase project is set up

### Development Phase Issues (When Implementation Begins)
1. **Supabase Connection**: Check `.env.local` has correct URL and API keys from `supabase status`
2. **AI Function Errors**: Validate function schemas match implementation, check OpenAI API key
3. **Real-time Updates**: Verify RLS policies allow subscriptions, check Supabase realtime settings
4. **File Upload Issues**: Check Supabase Storage bucket permissions and RLS policies
5. **TypeScript Errors**: Regenerate types with `npm run db:generate-types` after schema changes

### Debug Tools
- **Supabase Dashboard**: Database inspection, RLS policy testing, storage management
- **Supabase CLI**: `supabase status`, `supabase db remote commit` for migrations
- **OpenAI Playground**: Test prompts and function calling independently
- **Vercel Dashboard**: Logs, environment variables, deployment status
- **Browser DevTools**: Network tab for API calls, Console for errors, React DevTools for components

## Implementation Principles Summary

### Chat-First Philosophy
- All primary interactions through chat interface
- CRM views are read-mostly with nudges back to chat
- AI writes to database, humans read from it
- Show what the AI did transparently

### Event-Driven Architecture
```
User Chat → AI Processing → Function Calls → Database Events → UI Updates
```
- Every meaningful action creates an event record
- Events provide audit trail and enable coaching insights
- UI components subscribe to real-time event updates

### Progressive Trust Building
- Start with low-impact AI actions (logging notes, creating contacts)
- Build to higher-impact actions (updating deal status, setting priorities)
- Always show clear audit trails
- Provide rollback/undo capabilities
- Allow manual override for all operations

### Code Quality Standards
- Use TypeScript strict mode
- Implement proper error boundaries
- Add comprehensive error handling for AI operations
- Follow ESLint configuration
- Use Prettier for consistent formatting
- Write tests for critical AI validation logic

---

**Last Updated**: December 2024
**Project Status**: Planning Phase - No Implementation Yet
**Next Step**: Review REALCOACH_DEVELOPMENT_PLAN.md and await user approval to begin Phase 1

For complete technical specification and detailed architecture, see `REALCOACH_DEVELOPMENT_PLAN.md`.