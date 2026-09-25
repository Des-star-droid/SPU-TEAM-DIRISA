# IEC 2016 Voter Registration Data Extraction Approach

## Overview
This document outlines the approach for extracting IEC (Independent Electoral Commission) voter registration statistics for 2016 from the IEC website's dynamic web interface.

## Network Assessment

### Initial Exploration (Cell 2)
We assessed the IEC website's network behavior by:

1. **Establishing HTTP Connection**
   - Target: `https://www.elections.org.za/pw/StatsData/Voter-Registration-Statistics`
   - Status: ✅ HTTP 200 OK
   - Response Type: `text/html; charset=utf-8`
   - Response Size: ~131 KB

2. **Session Management**
   - Established an HTTP session to maintain state across multiple requests
   - Sessions handle cookies and connection pooling for efficient extraction

### Key Findings

The IEC website uses **ASP.NET Web Forms** architecture, which means:
- Data is not directly embedded in initial HTML
- Province/municipality/ward selections trigger **asynchronous POST requests**
- Each selection carries form state fields:
  - `__VIEWSTATE` (serialized page state)
  - `__EVENTVALIDATION` (security token)
  - Event target and argument parameters

## Extraction Strategy

### Scope
- **Province**: KwaZulu-Natal (Province ID: 4)
- **Year**: 2016
- **Geographic levels**: Province → Municipality → Ward

### Extraction Flow
```
IEC Website
    ↓
Initial Page Load (get __VIEWSTATE, __EVENTVALIDATION)
    ↓
Select Province (KwaZulu-Natal)
    ↓
Extract Available Municipalities
    ↓
For Each Municipality:
    - Select Municipality
    - Extract Available Wards
    ↓
For Each Ward (2016):
    - Extract Voter Registration Statistics
    ↓
Aggregate & Save Data
```

### Implementation Approach

**Cell 2** (Completed): Connection & Session Setup
- Load IEC website
- Extract initial form state
- Verify connectivity

**Cell 3** (Next): Province Selection & Municipality Extraction
- Send POST request with KwaZulu-Natal selection
- Parse response to extract municipality list
- Build data structure for ward-level extraction

**Cell 4+** (Subsequent): Ward-level Data Extraction
- Iterate through municipalities
- Extract wards for each municipality
- Retrieve 2016 voter registration statistics for each ward
- Combine into consolidated dataset

## Data Structure

The extracted data will contain:
- **Province**: KwaZulu-Natal
- **Municipalities**: eThekwini, Umlazi, Ntuzuma, Mariannridge (and others)
- **Wards**: Sub-divisions of municipalities
- **Registration Stats (2016)**:
  - Total registered voters
  - Demographic breakdowns (where available)
  - Registration rate changes
  - Age group distributions

## Output

The extracted data will be saved to:
```
data/raw/IEC_2016.CSV
```

This raw file will then be available for:
- Data cleaning and normalization
- Transformation and feature engineering
- Analysis of voter registration patterns
- Context for youth voter participation research (HSRC study)

## References
- IEC Official Source: https://www.elections.org.za/pw/StatsData/Voter-Registration-Statistics
- Related Research: HSRC "Your Voice, Your Choice" study (eThekwini, 2023)
