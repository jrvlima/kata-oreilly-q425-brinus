# ADR-001: Adopt Open Mobility Foundation Standards (MDS and CDS)

## Status
Accepted

## Context

MobilityCorp operates a multi-location electric vehicle rental service (scooters, eBikes, cars, vans) across multiple EU countries. The system must:

- Comply with EU/GDPR regulations and city-specific mobility policies
- Manage vehicle locations, status, and availability in real-time
- Track designated parking bay usage and enforce return location compliance
- Provide data for city authorities and internal analytics
- Support geo-restrictions and speed-limited zones
- Scale across multiple cities and countries with varying regulations

Without standardized data formats and APIs, MobilityCorp would need to:
- Build custom integrations for each city's reporting requirements
- Create proprietary data models that may not align with regulatory expectations
- Maintain multiple data transformation layers
- Risk non-compliance with evolving mobility regulations

## Decision

We will adopt the Open Mobility Foundation's standards as the foundation for MobilityCorp's data architecture:

### 1. Mobility Data Specification (MDS) v2.0+

**Provider API**: MobilityCorp will implement the Provider API to report vehicle data to city authorities:
- Vehicle location, status, and availability
- Trip history (anonymized, no personal rider data)
- Fleet composition and operational status

**Policy API**: The system will consume city policies to:
- Enforce geographic zones (where vehicles can operate)
- Apply vehicle caps and speed limits
- Respond to dynamic policy changes (events, emergencies)

**Agency API**: Enable real-time collaboration with city officials for operational issues

### 2. Curb Data Specification (CDS)

**CURBS API**: Consume curb location and regulation data from cities:
- Designated parking bay locations
- Time-based restrictions
- Pricing information for different zones

**EVENTS API**: Report vehicle activities at curb locations:
- Vehicle arrivals and departures at parking bays
- Compliance with return location requirements
- Real-time occupancy status

**METRICS API**: Calculate and report usage statistics:
- Bay occupancy rates and dwell times
- Utilization patterns for AI-driven redistribution
- Compliance metrics

## Consequences

### Positive

**Regulatory Compliance**
- Built-in GDPR compliance (MDS excludes personal rider information)
- Meets city mobility reporting requirements out-of-the-box
- Reduces legal risk across multiple jurisdictions

**Interoperability**
- Standard APIs enable easier expansion to new cities
- Reduces integration effort (cities already support these standards)
- Facilitates data sharing with city partners

**Data Quality**
- Well-defined data models reduce ambiguity
- Industry-vetted schemas improve data consistency
- Structured data feeds AI/ML models more effectively

**Operational Efficiency**
- Reusable components across markets
- Reduced development and maintenance costs
- Access to open-source tooling and community support

**AI/Analytics Foundation**
- Standardized event streams for real-time processing
- Historical data in consistent format for ML training
- Metrics API provides pre-calculated KPIs

### Negative

**Standard Limitations**
- May need custom extensions for MobilityCorp-specific features
- Standards evolve (need to track MDS 2.1+ updates)
- Not all cities may support latest versions

**Implementation Overhead**
- Initial investment to implement both specifications
- Need to maintain compatibility as standards evolve
- Training staff on OMF standards and best practices

**Dependency Risk**
- Reliance on external standard bodies (OMF governance)
- Breaking changes in major version updates require migration
- Must participate in OMF community to influence roadmap

### Mitigation Strategies

1. **Extensibility Layer**: Build internal extensions on top of OMF standards for MobilityCorp-specific features
2. **Version Management**: Implement API versioning to support multiple MDS/CDS versions simultaneously
3. **Abstraction**: Create internal domain models that map to/from OMF standards (prevent vendor lock-in)
4. **Community Engagement**: Actively participate in OMF working groups to influence future standards

## Related Decisions

- ADR-002: Data Architecture and Domain Model Design (to be created)
- ADR-003: City Integration Strategy (to be created)
- ADR-004: Privacy and GDPR Compliance Approach (to be created)

## References

- [MDS Specification](https://www.openmobilityfoundation.org/about-mds/)
- [CDS Specification](https://www.openmobilityfoundation.org/about-cds/)
- [OMF GitHub Repository](https://github.com/openmobilityfoundation)
- [MDS Version 2.0 Documentation](https://github.com/openmobilityfoundation/mobility-data-specification)
- [CDS Working Group](https://github.com/openmobilityfoundation/curb-data-specification)

## Notes

**Date**: October 21, 2025
**Authors**: BRINUS Team (Rafael, Prashant, Khaled, João Vinagre)
**Reviewers**: To be reviewed by competition judges

**Key Business Impact**: This decision directly addresses MobilityCorp's need for:
- Compliance with EU city regulations
- Standardized data for AI-driven vehicle distribution optimization
- Scalable architecture for multi-city expansion
