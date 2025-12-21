# RealCoach.ai API Design

## API Architecture Overview

The RealCoach.ai API follows RESTful principles with AI-enhanced endpoints for natural language processing. All endpoints use JSON for request/response bodies and include proper authentication via Supabase Auth.

### Base URL
```
Production: https://realcoach-ai.vercel.app/api
Development: http://localhost:3000/api
```

### Authentication
All API endpoints require authentication via Supabase Auth JWT token:
```
Authorization: Bearer <jwt_token>
```

## Core API Endpoints

### Authentication Endpoints

#### POST `/api/auth/signup`
Create new user account
```typescript
interface SignupRequest {
  email: string;
  password: string;
  timezone?: string;
  market_focus?: string;
}

interface SignupResponse {
  user: User;
  session: Session;
}
```

#### POST `/api/auth/signin`
Authenticate existing user
```typescript
interface SigninRequest {
  email: string;
  password: string;
}

interface SigninResponse {
  user: User;
  session: Session;
}
```

### Chat Endpoints

#### POST `/api/chat/message`
Send message to AI coach (primary interaction endpoint)
```typescript
interface ChatMessageRequest {
  message: string;
  conversation_id?: string;
  context?: ConversationContext;
}

interface ChatMessageResponse {
  id: string;
  content: string;
  actions?: AIAction[];
  conversation_id: string;
  created_at: string;
}
```

#### GET `/api/chat/conversations`
Retrieve chat history
```typescript
interface ConversationsResponse {
  conversations: Conversation[];
  pagination: PaginationInfo;
}
```

#### GET `/api/chat/conversations/[id]/messages`
Get messages for specific conversation
```typescript
interface MessagesResponse {
  messages: Message[];
  conversation: Conversation;
}
```

### Contact Management

#### GET `/api/contacts`
List user's contacts with filtering and pagination
```typescript
interface ContactsQuery {
  page?: number;
  limit?: number;
  search?: string;
  type?: 'lead' | 'client' | 'referral';
  tags?: string[];
}

interface ContactsResponse {
  contacts: Contact[];
  pagination: PaginationInfo;
  total: number;
}
```

#### GET `/api/contacts/[id]`
Get specific contact details
```typescript
interface ContactResponse {
  contact: Contact;
  recent_events: Event[];
  related_deals: Deal[];
}
```

#### POST `/api/contacts`
Create new contact (typically via AI, but manual option available)
```typescript
interface CreateContactRequest {
  first_name: string;
  last_name?: string;
  email?: string;
  phone?: string;
  address?: Address;
  contact_type?: 'lead' | 'client' | 'referral';
  source?: string;
  notes?: string;
}

interface ContactResponse {
  contact: Contact;
  event: Event; // Created event record
}
```

#### PUT `/api/contacts/[id]`
Update contact information
```typescript
interface UpdateContactRequest {
  first_name?: string;
  last_name?: string;
  email?: string;
  phone?: string;
  address?: Address;
  contact_type?: 'lead' | 'client' | 'referral';
  notes?: string;
  tags?: string[];
}
```

### Deal Management

#### GET `/api/deals`
List user's deals with filtering
```typescript
interface DealsQuery {
  page?: number;
  limit?: number;
  status?: 'prospecting' | 'active' | 'under_contract' | 'closed' | 'dead';
  deal_type?: 'buy' | 'sell' | 'rent';
  contact_id?: string;
}

interface DealsResponse {
  deals: Deal[];
  pipeline_summary: PipelineSummary;
  pagination: PaginationInfo;
}
```

#### POST `/api/deals`
Create new deal
```typescript
interface CreateDealRequest {
  contact_id: string;
  title: string;
  deal_type: 'buy' | 'sell' | 'rent';
  value?: number;
  commission_rate?: number;
  expected_close_date?: string;
  address?: Address;
  notes?: string;
}
```

#### PUT `/api/deals/[id]/status`
Update deal status
```typescript
interface UpdateDealStatusRequest {
  status: 'prospecting' | 'active' | 'under_contract' | 'closed' | 'dead';
  notes?: string;
  actual_close_date?: string;
}
```

### Events & Timeline

#### GET `/api/events`
Get timeline of events with filtering
```typescript
interface EventsQuery {
  page?: number;
  limit?: number;
  contact_id?: string;
  deal_id?: string;
  event_type?: string;
  from_date?: string;
  to_date?: string;
}

interface EventsResponse {
  events: Event[];
  pagination: PaginationInfo;
}
```

#### POST `/api/events`
Create new event (typically via AI)
```typescript
interface CreateEventRequest {
  contact_id?: string;
  deal_id?: string;
  event_type: string;
  event_data: Record<string, any>;
  notes?: string;
}
```

### File Processing

#### POST `/api/files/upload`
Upload file for processing
```typescript
interface FileUploadRequest {
  // Multipart form data
  file: File;
  type: 'contacts' | 'documents';
}

interface FileUploadResponse {
  upload_id: string;
  filename: string;
  processing_status: 'pending' | 'processing' | 'completed' | 'failed';
}
```

#### GET `/api/files/[upload_id]/status`
Check file processing status
```typescript
interface FileStatusResponse {
  upload_id: string;
  filename: string;
  processing_status: 'pending' | 'processing' | 'completed' | 'failed';
  progress?: number;
  results?: ProcessingResults;
  errors?: string[];
}
```

#### POST `/api/files/[upload_id]/confirm`
Confirm import after preview
```typescript
interface ConfirmImportRequest {
  confirmed_contacts: string[]; // Array of contact IDs to import
  skip_contacts: string[];      // Array of contact IDs to skip
}
```

### Coaching & Summaries

#### GET `/api/coaching/summaries`
Get daily/weekly summaries
```typescript
interface SummariesQuery {
  type?: 'daily' | 'weekly';
  from_date?: string;
  to_date?: string;
  limit?: number;
}

interface SummariesResponse {
  summaries: Summary[];
  pagination: PaginationInfo;
}
```

#### GET `/api/coaching/insights`
Get current insights and recommendations
```typescript
interface InsightsResponse {
  priorities: Priority[];
  stale_contacts: Contact[];
  deal_risks: DealRisk[];
  opportunities: Opportunity[];
  patterns: Pattern[];
}
```

#### POST `/api/coaching/generate-summary`
Manually trigger summary generation
```typescript
interface GenerateSummaryRequest {
  type: 'daily' | 'weekly';
  date?: string; // Defaults to today
}

interface GenerateSummaryResponse {
  summary: Summary;
  generated_at: string;
}
```

### Subscription Management

#### GET `/api/subscription`
Get current subscription status
```typescript
interface SubscriptionResponse {
  subscription: {
    id: string;
    status: 'active' | 'canceled' | 'past_due' | 'trial';
    current_period_start: string;
    current_period_end: string;
    plan: SubscriptionPlan;
  };
  usage: UsageStats;
}
```

#### POST `/api/subscription/checkout`
Create Stripe checkout session
```typescript
interface CheckoutRequest {
  plan_id: string;
  success_url: string;
  cancel_url: string;
}

interface CheckoutResponse {
  checkout_url: string;
  session_id: string;
}
```

### Analytics & Reporting

#### GET `/api/analytics/dashboard`
Get dashboard analytics
```typescript
interface DashboardResponse {
  total_contacts: number;
  active_deals: number;
  recent_activities: number;
  conversion_rate: number;
  pipeline_value: number;
  monthly_stats: MonthlyStats;
}
```

## AI Function Calling Schema

The AI system uses structured function calling to interact with the database. These functions are not directly exposed as REST endpoints but are used internally by the chat system.

### Contact Functions

```typescript
// Create contact from natural language
const createContact = {
  name: 'create_contact',
  description: 'Create a new contact in the CRM',
  parameters: {
    type: 'object',
    properties: {
      first_name: { type: 'string' },
      last_name: { type: 'string' },
      email: { type: 'string', format: 'email' },
      phone: { type: 'string' },
      contact_type: {
        type: 'string',
        enum: ['lead', 'client', 'referral']
      },
      source: { type: 'string' },
      notes: { type: 'string' }
    },
    required: ['first_name']
  }
};

// Update contact information
const updateContact = {
  name: 'update_contact',
  description: 'Update existing contact information',
  parameters: {
    type: 'object',
    properties: {
      contact_id: { type: 'string' },
      updates: {
        type: 'object',
        properties: {
          first_name: { type: 'string' },
          last_name: { type: 'string' },
          email: { type: 'string' },
          phone: { type: 'string' },
          contact_type: { type: 'string' },
          notes: { type: 'string' }
        }
      }
    },
    required: ['contact_id', 'updates']
  }
};
```

### Deal Functions

```typescript
// Create new deal
const createDeal = {
  name: 'create_deal',
  description: 'Create a new deal in the pipeline',
  parameters: {
    type: 'object',
    properties: {
      contact_id: { type: 'string' },
      title: { type: 'string' },
      deal_type: {
        type: 'string',
        enum: ['buy', 'sell', 'rent']
      },
      value: { type: 'number' },
      commission_rate: { type: 'number' },
      expected_close_date: { type: 'string', format: 'date' },
      address: {
        type: 'object',
        properties: {
          street: { type: 'string' },
          city: { type: 'string' },
          state: { type: 'string' },
          zip: { type: 'string' }
        }
      }
    },
    required: ['contact_id', 'title', 'deal_type']
  }
};

// Update deal status
const updateDealStatus = {
  name: 'update_deal_status',
  description: 'Update the status of an existing deal',
  parameters: {
    type: 'object',
    properties: {
      deal_id: { type: 'string' },
      status: {
        type: 'string',
        enum: ['prospecting', 'active', 'under_contract', 'closed', 'dead']
      },
      notes: { type: 'string' },
      actual_close_date: { type: 'string', format: 'date' }
    },
    required: ['deal_id', 'status']
  }
};
```

### Event Functions

```typescript
// Log activity/event
const logEvent = {
  name: 'log_event',
  description: 'Log an activity or event',
  parameters: {
    type: 'object',
    properties: {
      contact_id: { type: 'string' },
      deal_id: { type: 'string' },
      event_type: {
        type: 'string',
        enum: ['call', 'email', 'meeting', 'showing', 'note', 'follow_up']
      },
      event_data: {
        type: 'object',
        properties: {
          duration: { type: 'number' },
          outcome: { type: 'string' },
          next_steps: { type: 'string' },
          notes: { type: 'string' }
        }
      }
    },
    required: ['event_type', 'event_data']
  }
};
```

## Error Handling

All endpoints return standardized error responses:

```typescript
interface ErrorResponse {
  error: {
    code: string;
    message: string;
    details?: any;
  };
  timestamp: string;
  request_id: string;
}
```

### Common Error Codes
- `UNAUTHORIZED`: Invalid or missing authentication
- `FORBIDDEN`: Insufficient permissions
- `NOT_FOUND`: Resource not found
- `VALIDATION_ERROR`: Invalid request data
- `AI_ERROR`: AI processing failed
- `RATE_LIMITED`: Too many requests
- `INTERNAL_ERROR`: Server error

## Rate Limiting

API endpoints are rate limited to prevent abuse:
- **Chat endpoints**: 60 requests per minute per user
- **CRUD endpoints**: 1000 requests per hour per user
- **File upload**: 10 uploads per hour per user

## Webhooks

### Stripe Webhooks

#### POST `/api/webhooks/stripe`
Handle Stripe subscription events
- `invoice.payment_succeeded`
- `invoice.payment_failed`
- `customer.subscription.updated`
- `customer.subscription.deleted`

### Internal Webhooks

#### POST `/api/webhooks/supabase`
Handle Supabase real-time events (if needed)
- Database change notifications
- Auth events

---

This API design provides a comprehensive foundation for RealCoach.ai with clear separation between AI-enhanced chat functionality and traditional CRM operations, while maintaining RESTful principles and proper authentication throughout.