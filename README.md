# OutersaleBackend - Documentation

A Django-based API system for collecting and managing organization and people data from Apollo.io with real-time progress tracking and comprehensive filtering capabilities.

## 🚀 What This System Does

OutersaleBackend automates the process of collecting business intelligence data by:

- **Converting Apollo.io URLs** into automated data collection tasks
- **Extracting organization profiles** with complete company information
- **Gathering people data** including contact details and employment history
- **Providing real-time monitoring** of data collection progress
- **Offering advanced search** and filtering across collected data

## 🏗️ System Overview

### Core Components

- **ChromeApi** - Handles Apollo.io URL processing and manages scraping tasks
- **OrganizationApi** - Manages company data with advanced filtering capabilities  
- **PeopleApi** - Handles contact information and people search functionality

### Data Flow

1. **Input**: Apollo.io filter URL (e.g., companies in specific locations/industries)
2. **Processing**: System converts URL into structured data collection tasks
3. **Collection**: Automated scraping with progress tracking and error handling
4. **Storage**: Data stored in MongoDB with complete field preservation
5. **Access**: RESTful APIs for searching, filtering, and retrieving data

## 📚 API Overview

### Task Management - ChromeApi

Start and control data collection tasks:

```bash
# Start collecting data from Apollo URL
POST /api/chrome/apollo-url/
{
  "apollo_url": "https://app.apollo.io/#/companies?organizationLocations[]=United%20States",
  "task_name": "US Companies Collection"
}
```

```bash
# Monitor progress in real-time
GET /api/chrome/scraping-control/
```

```bash
# Control running tasks
POST /api/chrome/scraping-control/
{
  "action": "pause"  # or "resume", "stop"
}
```

### Data Retrieval - Task Mapping

Access collected data by task:

```bash
# List all completed tasks
GET /api/taskmapping/
```

```bash
# Get organizations and people data for a specific task
POST /api/taskmapping/
{
  "task_ids": ["6842b07f65c07db3d81ba8c8", "6842ae4a65c07db3d81ba788"],
  "data_type": "organizations",  // or "people"
  "page": 1,
  "page_size": 25
}
```

**Response includes:**
- Complete organization profiles
- Associated people/contacts for each organization
- Employment history and contact verification status

### Organization Search - OrganizationApi

Advanced filtering and search across organization data:

```bash
# Filter by location and company size
GET /api/organizations/?city=San%20Francisco&employeeRange=51-200

# Search multiple companies at once
GET /api/organizations/?search=Microsoft,Google,Apple

# Industry and keyword filtering
GET /api/organizations/?industries=technology&keywords=SaaS,cloud
```

### People Search - PeopleApi

Find contacts with detailed filtering:

```bash
# Find executives in specific location
GET /api/people/?title=CEO&city=New%20York&has_email=true

# Search by company and department
GET /api/people/?company=Microsoft&departments=engineering&seniority=senior
```

## 🎯 Usage Examples

### Example 1: Collecting Tech Companies in California

**Step 1: Start Collection**
```bash
curl -X POST http://<your-ip>:8000/api/chrome/apollo-url/ \
  -H "Content-Type: application/json" \
  -d '{
    "apollo_url": "https://app.apollo.io/#/companies?organizationLocations[]=California&organizationIndustryTagIds[]=tech_industry_id",
    "task_name": "California Tech Companies"
  }'
```

**Response:**
```json
{
  "success": true,
  "task_id": "674a1b2c8d9e1f2a3b4c5d6e",
  "task_name": "California Tech Companies",
  "search_summary": "Locations: California; Industries: Technology"
}
```

**Step 2: Monitor Progress**
```bash
curl http://<your-ip>:8000/api/chrome/scraping-control/
```

**Response:**
```json
{
  "is_running": true,
  "session_data": {
    "organizations_found": 245,
    "organizations_saved": 230,
    "people_total_added": 1150,
    "industries_completed": 3,
    "total_industries": 8
  }
}
```

**Step 3: Retrieve Data**
```bash
curl -X POST http://<your-ip>:8000/api/taskmapping/ \
  -H "Content-Type: application/json" \
  -d '{
  "task_ids": ["6842b07f65c07db3d81ba8c8", "6842ae4a65c07db3d81ba788"],
  "data_type": "organizations",  // or "people"
  "page": 1,
  "page_size": 25
}'
```

### Example 2: Finding CEOs in Specific Industries

```bash
# Search for CEOs in fintech companies
GET http://<your-ip>:8000/api/people/?title=CEO&company_industries=fintech&city=San%20Francisco&has_linkedin=true
```

**Response includes:**
- CEO contact information
- Company details
- Email verification status
- LinkedIn profiles
- Employment history

### Example 3: Company Research with Multiple Filters

```bash
# Find medium-sized SaaS companies founded after 2015
GET http://<your-ip>:8000/api/organizations/?employeeRange=51-200&keywords=SaaS&founded_year_min=2015&has_linkedin=true
```

**Response includes:**
- Complete company profiles
- Employee count and growth metrics
- Industry classifications
- Contact information
- Social media presence

## 📊 Data Structure

### Organization Data
Each organization record includes:
- **Basic Information**: Name, website, contact details
- **Business Details**: Industry, employee count, revenue data
- **Location**: City, state, country, full address
- **Social Presence**: LinkedIn, Twitter, Facebook profiles
- **Growth Metrics**: Employee growth rates, funding information
- **Apollo Metadata**: All available fields from Apollo.io

### People Data
Each person record includes:
- **Personal Information**: Name, title, contact details
- **Employment**: Current role, employment history, company details
- **Contact Status**: Email verification, LinkedIn presence
- **Location**: Geographic information
- **Professional Details**: Departments, seniority level, functions

## 🔍 Advanced Features

### Smart Data Collection
- **Automatic Pagination**: Handles large datasets efficiently
- **Industry Splitting**: Breaks down large searches by industry for better performance
- **Resume Capability**: Continue interrupted collection sessions
- **Rate Limit Handling**: Automatic compliance with Apollo.io limits

### Real-time Monitoring
- **Live Progress Tracking**: See organizations and people being collected in real-time
- **Task Control**: Pause, resume, or stop collection at any time
- **Error Handling**: Automatic retry on temporary failures
- **Status Persistence**: Progress saved to database for reliability

### Comprehensive Search
- **Multi-field Filtering**: Combine location, size, industry, and custom criteria
- **Text Search**: Search across company names, descriptions, and keywords
- **Pagination Support**: Handle large result sets efficiently
- **Sorting Options**: Sort by relevance, size, date, or custom criteria

## 🚦 System Status & Control

### Task States
- **started**: Task created and beginning data collection
- **running**: Actively collecting data
- **paused**: Temporarily stopped, can be resumed
- **stopped**: Permanently ended by user
- **completed**: Successfully finished data collection
- **completed_with_people**: Organization and people data collected

### Progress Tracking
The system provides detailed progress information:
- Organizations found vs saved
- People contacts discovered
- Current processing status
- Industry-by-industry breakdown (for large tasks)
- Estimated completion time

### Control Operations
- **Pause**: Temporarily halt collection (can resume later)
- **Resume**: Continue from where paused
- **Stop**: Permanently end collection
- **Status**: Get detailed progress and performance metrics

## 📋 Requirements

### System Requirements
- Python 3.8+
- Django 4.2+
- MongoDB 4.0+
- Minimum 4GB RAM for large collections

### External Dependencies
- Valid Apollo.io account for data access
- Prospeo API key for email discovery (optional)
- Stable internet connection for data collection

### Supported Apollo.io Filters
The system can process Apollo URLs with these filters:
- **Locations**: organizationLocations[]
- **Employee Ranges**: organizationNumEmployeesRanges[]
- **Industries**: organizationIndustryTagIds[]
- **Keywords**: qOrganizationKeywordTags[]
- **Company Lists**: organizationIds[]

## 🔧 Performance & Scalability

### Collection Performance
- **Rate**: 200-500 organizations per hour (depending on data size)
- **People**: 50-100 contacts per organization
- **Efficiency**: Smart batching and parallel processing
- **Reliability**: Automatic retry and error recovery

### Data Volume Handling
- **Small Tasks**: <1000 organizations - Direct processing
- **Large Tasks**: >1000 organizations - Industry-based splitting
- **Resume Support**: Continue large tasks if interrupted
- **Progress Persistence**: All progress saved to database

### API Performance
- **Response Time**: <200ms for filtered searches
- **Pagination**: 25-100 results per page
- **Concurrent Users**: Supports multiple simultaneous API users
- **Caching**: Intelligent caching for improved performance

## 📈 Use Cases

### Lead Generation
- Collect prospects by industry, location, and company size
- Find decision-makers with verified contact information
- Build targeted lists for sales outreach
- Track and organize prospect data

### Market Research
- Analyze company distributions by geography and industry
- Study employee count trends and growth patterns
- Research competitor landscapes
- Build comprehensive market databases

### Recruitment
- Find companies in specific industries and locations
- Identify potential candidates by role and seniority
- Build talent pools for specific skills and functions
- Research company cultures and growth trajectories

### Business Intelligence
- Monitor industry trends and company movements
- Track competitor hiring and expansion patterns
- Analyze market opportunities by region
- Build comprehensive business databases

## 📖 API Documentation

### Interactive Documentation
- **Swagger UI**: Complete API reference with examples
- **ReDoc**: Alternative documentation format
- **Live Testing**: Test API endpoints directly from documentation

### Response Formats
All APIs return JSON with consistent structure:
- **Success Responses**: Data with metadata and pagination
- **Error Responses**: Detailed error messages and codes
- **Status Responses**: Real-time progress and state information

---

**Note**: This system is designed for legitimate business intelligence and lead generation purposes. Users are responsible for ensuring compliance with Apollo.io's terms of service and applicable data protection regulations.
