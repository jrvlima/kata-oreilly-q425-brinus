# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains the architectural solution for the **O'Reilly Q4 2025 Architectural Katas Challenge**, focused on AI-enabled architecture for MobilityCorp. The team (BRINUS) is designing a mobility platform for short-term vehicle rentals including electric scooters, eBikes, electric cars, and vans.

**Team Members:** Rafael, Prashant, Khaled, João Vinagre

**Competition Schedule:**
- Solutions due: October 23, 2025 (11:59pm ET)
- Semifinals: November 5, 2025
- Finals: November 13, 2025

## Repository Structure

```
/originals/          - Original PDF/DOCX specification documents
/fleeting/           - Markdown conversions and working documents
  /Requirements/     - Software and technical requirements
  /DataModel/        - Logical data model documentation
  /assets/           - Images and diagrams
/adrs/               - Architecture Decision Records (to be created)
/diagrams/           - Architecture diagrams (to be created)
  /c4/               - C4 model diagrams
  /uml/              - UML diagrams
```

## Key Project Context

### MobilityCorp Business Problem

MobilityCorp operates across multiple EU locations with multi-language and multi-currency support. Key challenges:

1. **Vehicle Availability**: Vehicles are often not in the right locations when customers need them
2. **Battery Management**: EVs frequently run out of charge; need to prioritize battery swaps
3. **Customer Engagement**: Most usage is ad-hoc; goal is to increase regular/commute usage

### Vehicle Types & Booking Rules

- **Cars & Vans**: Bookable up to 7 days in advance, fixed duration rentals
- **Scooters & Bikes**: Bookable up to 30 minutes in advance, open-ended (up to 12 hours)

### Core Data Entities

See `fleeting/DataModel/MobilityCorp_Logical_Data_Model.md` for complete schema:
- **ParkingSpot**: Location data and occupancy
- **Vehicle**: GPS tracking, charge levels, operational status
- **Booking**: Rental transactions and timing
- **ChargingSession**: Battery/charging event tracking
- **User**: Customer accounts with localization preferences

## Competition Deliverables

Teams must submit to GitHub by deadline:

1. **Overview**: Narrative describing AI usage to solve MobilityCorp's problems
2. **Diagrams**: Comprehensive and targeted views for each AI use case
3. **ADRs**: Architecture Decision Records for AI-related implementations with trade-off analysis
4. **Implementation Details** (optional)
5. **Video Presentation** (5 minutes, for semifinalists only)

### Judging Criteria

- Innovative use of Generative AI in solution(s)
- Suitability of solution given constraints
- Appropriate levels of detail
- Dealing with uncertainty in AI technology
- Matching architectural characteristics with existing architecture
- Validation and verification of AI results
- **Communication is key**: Clear diagrams, well-documented decisions

## Documentation Standards

### Architecture Decision Records (ADRs)

Use ADRs to document all significant architectural decisions, especially AI-related choices. Store in `/adrs/` directory.

Reference: https://github.com/joelparkerhenderson/architecture-decision-record

### Diagram Guidelines

From competition guidance:
- Use **C4 Model** for system architecture (https://c4model.com/)
- Keep diagrams simple and understandable (boxes, arrows, words)
- Include legends/keys if using different shapes with specific meanings
- Focus on conveying clear and shared understanding
- Consider using Structurizr or LikeC4 for diagram generation

Quote from judges: *"The goal of a diagram is to convey a clear and shared understanding of the architecture"* - Neal Ford

## AI Architecture Considerations

Key aspects to address in the solution:

1. **Uncertainty Handling**: AI is changing fast; design for model/provider flexibility
2. **Provider Risk**: Plan for provider price changes or shutdowns
3. **Validation**: How to verify non-deterministic GenAI functionality in production
4. **Integration**: Ensure AI-driven components match existing architectural characteristics

## Communication Channel

Team collaboration via:
- Discord: https://discord.gg/kzD26QWS (Server: 1427674179335094415)
- Google Groups: Q4Qatas2025-BRINUS

## Recommended Resources

From O'Reilly follow-up materials:
- [AI Engineering](https://learning.oreilly.com/library/view/ai-engineering/9781098166298/)
- [Fundamentals of Software Architecture, 2nd ed](https://learning.oreilly.com/library/view/fundamentals-of-software/9781098175504/)
- [Building Microservices, 2nd ed](https://learning.oreilly.com/library/view/building-microservices-2nd/9781492034018/)
- [Generative AI Design Patterns](https://learning.oreilly.com/library/view/generative-ai-design/9798341622654/)

## Working with This Repository

### Key Reference Documents

Always refer to these for context:
- `Q4Qata2025.md` - Complete competition problem statement
- `qata-description.md` - Competition overview and schedule
- `fleeting/Requirements/MobilityCorp_Software_Requirements_Document.md` - Functional requirements
- `fleeting/DataModel/MobilityCorp_Logical_Data_Model.md` - Data schema

### File Organization

- Keep original specification files in `/originals/` untouched
- Place working documents and converted content in `/fleeting/`
- Store all architecture diagrams in `/diagrams/` with appropriate subdirectories
- Document decisions in `/adrs/` using ADR format

### Git Workflow

This is an active competition project. Main branch: `main`

Current untracked files to address:
- `Q4Qata2025.md` (working document)
- `qata-description.md` (competition description)
- `Architecture Design Approaches-2025-10-18-183716.png` (diagram)
