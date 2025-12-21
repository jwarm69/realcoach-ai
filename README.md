# RealCoach.ai

> Chat-first AI coaching + lightweight CRM for real estate agents

**Status**: Pre-development planning phase
**Timeline**: 12-week MVP development plan
**Tech Stack**: Next.js 14, Supabase, OpenAI GPT-4, Stripe

## Overview

RealCoach.ai solves the critical problem of CRM adoption for solo real estate agents by replacing tedious form-filling with natural conversation. Agents chat with an AI coach that automatically maintains their CRM while providing contextual coaching based on real activity patterns.

### Core Value Proposition
- **Chat-first interface**: Updates via natural conversation, not forms
- **Automatic CRM maintenance**: AI writes to database based on conversation
- **Contextual coaching**: Daily/weekly guidance based on actual activity
- **Event-driven architecture**: Everything flows through conversational updates

## Quick Start (Development)

> **Note**: Project is currently in planning phase. Development starts after client approval.

```bash
# When ready to begin development:
cd realcoach-ai
npm create-next-app@latest . --typescript --tailwind --eslint --app
npm install @supabase/supabase-js stripe openai

# See REALCOACH_DEVELOPMENT_PLAN.md for complete setup instructions
```

## Project Structure

```
realcoach-ai/
├── REALCOACH_DEVELOPMENT_PLAN.md  # Complete technical specification
├── docs/                          # Technical documentation
├── .github/workflows/             # CI/CD configuration
└── [Next.js project files when created]
```

## Key Features (MVP - Iteration 1)

### 🤖 AI Coach Chat
- Natural language CRM updates
- Intent classification and database operations
- Context-aware responses with memory
- File upload support (CSV, vCard, PDF)

### 📊 Event-Driven CRM
- Automatic contact and deal management
- Timeline view of all activities
- Minimal form-based editing (nudges back to chat)
- Real-time data synchronization

### 📈 Intelligent Coaching
- Daily summaries with priorities
- Weekly pattern analysis
- Proactive follow-up reminders
- Deal pipeline health insights

### 💳 Simple Monetization
- Monthly/annual subscription plans
- Stripe integration
- No usage limits or feature gating

## Technical Architecture

### Core Components
- **Frontend**: Next.js 14 with App Router and TypeScript
- **Database**: Supabase (PostgreSQL with RLS)
- **AI**: OpenAI GPT-4 with function calling
- **Auth**: Supabase Auth (email/password)
- **Payments**: Stripe subscriptions
- **Deployment**: Vercel with cron jobs
- **Storage**: Supabase Storage for file uploads

### Key Patterns
- **Event Sourcing**: All CRM changes flow through events table
- **AI Orchestration**: Function calling for structured database operations
- **Progressive Enhancement**: Chat → CRM → Coaching → Automation
- **Optimistic UI**: Immediate updates with rollback capabilities

## Development Timeline

| Phase | Duration | Focus | Key Deliverables |
|-------|----------|-------|------------------|
| **Foundation** | Weeks 1-2 | Project setup, auth, database | Functional auth + database schema |
| **AI System** | Weeks 3-4 | Chat interface, AI integration | Working AI-database integration |
| **CRM Backbone** | Weeks 5-6 | Event system, CRM views | Functional CRM with event sourcing |
| **File Processing** | Weeks 7-8 | Upload system, parsers | Contact import workflows |
| **Coaching** | Weeks 9-10 | Summaries, insights, automation | Daily/weekly coaching features |
| **Launch Prep** | Weeks 11-12 | Payments, testing, deployment | Production-ready application |

## Success Metrics

### Technical
- **AI Accuracy**: >90% successful function calls
- **Performance**: <2s average chat response time
- **Reliability**: <1% error rate for database operations

### Product
- **Daily Usage**: Active chat interaction
- **Trust Building**: Users reference AI memory unprompted
- **CRM Adoption**: Database updated without manual entry

### Business
- **Retention**: >60% active after 30 days
- **Engagement**: 10+ daily chat messages per active user
- **Conversion**: >20% trial-to-paid conversion

## Risk Management

### Critical Risks
1. **AI Data Corruption**: Mitigated with validation layers and audit trails
2. **User Adoption**: Addressed through gradual trust building and transparency
3. **API Cost Scaling**: Managed with optimization and tiered pricing

### Development Risks
4. **Context Complexity**: Structured storage and summarization
5. **File Processing**: Robust validation and manual fallbacks
6. **Performance**: Async processing and query optimization

## Next Steps

### Immediate (Post-Planning)
1. **Environment Setup**: Next.js project + Supabase configuration
2. **Database Schema**: Implement tables with RLS policies
3. **AI Prototype**: Basic OpenAI integration with function calling
4. **Authentication Flow**: Supabase Auth implementation

### Week 1 Goals
- [ ] Next.js 14 project initialized
- [ ] Supabase project configured
- [ ] Database schema deployed
- [ ] Basic auth flow working
- [ ] Development environment established

## Documentation

- **[Complete Development Plan](./REALCOACH_DEVELOPMENT_PLAN.md)**: Comprehensive technical specification
- **[Project Instructions](./CLAUDE.md)**: Claude-specific development guidelines
- **[API Documentation](./docs/api.md)**: API endpoints and schemas *(created during development)*
- **[Database Schema](./docs/database.md)**: Complete database design *(created during development)*

## Client Information

**Project Value**: $15,000+ professional development project
**Client Profile**: Real estate coaching business creating agent productivity tools
**Market**: Solo and small-team real estate agents seeking CRM solutions
**Unique Value**: AI-native approach to CRM adoption challenges

---

**Contact**: Development managed through Claude Code sessions
**Status**: Ready for development phase pending client approval
**Last Updated**: December 2024