# NBA Analytics Data Lake  
![](/Assets/thumbnail.png)
<details>
<summary>Quick Navigation</summary>

- [NBA Analytics Data Lake](#nba-analytics-data-lake)
  - [Project Overview](#project-overview)
    - [Objective](#objective)
    - [Features](#features)
  - [Architecture](#architecture)
    - [System Design](#system-design)
    - [Workflow](#workflow)
  - [Technologies Used](#technologies-used)
  - [Project Structure](#project-structure)
  - [Prerequisites](#prerequisites)
  - [How to Setup](#how-to-setup)
    - [Create IAM Policy](#create-iam-policy)
    - [Launch CloudShell](#launch-cloudshell)
    - [Configure Environment](#configure-environment)
    - [Create Script File](#create-script-file)
  - [Deployment](#deployment)
    - [Install Dependencies](#install-dependencies)
    - [Run Script](#run-script)
  - [Query Demo](#query-demo)
  - [Validation](#validation)
  - [Security Considerations](#security-considerations)
  - [Troubleshooting](#troubleshooting)
  - [Future Enhancements](#future-enhancements)
  - [Blog🔗](#blog)
  - [Contributing](#contributing)
  - [License](#license)
</details>

## Project Overview
### Objective

Build an **automated NBA analytics pipeline** that: 

1. Ingests raw player data via API  
2. Stores it in a cloud-native data lake  
3. Enables SQL analytics without data movement  
4. Serves as a foundation for sports betting/analytics applications

### Features

- **Serverless Infrastructure**: Zero servers to manage (S3 + Glue + Athena)  
- **Real-Time Schema Discovery**: Auto-catalog JSON data with AWS Glue  
- **Cost-Efficient Queries**: $5/TB scanned via Amazon Athena  
- **Scalable Storage**: Handle 10,000+ player records with S3  


## Architecture  
![architecture](/Assets//aws-nba-dat-lake.png)  

### System Design  

1. **Data Source**: [SportsDataIO API](https://sportsdata.io/) for NBA player statistics.  
2. **Data Ingestion**: Python script (`boto3`) for API integration and S3 uploads.  
3. **Storage Layer**: AWS S3 bucket for raw JSON data and query results.  
4. **Metadata Catalog**: AWS Glue for schema discovery and table creation.  
5. **Query Layer**: Amazon Athena for serverless SQL analytics.  


### Workflow  

1. Python script fetches NBA player data from SportsDataIO API.  
2. Raw JSON is uploaded to an S3 bucket (`s3://<bucket>/raw-data/`).  
3. AWS Glue crawler auto-discovers schema and creates metadata tables.  
4. Analysts run SQL queries directly on S3 data via Athena.  
 ![workflow](/Assets//workflow.png)

## Technologies Used  
| Category         | Technologies              |  
|------------------|---------------------------|  
| Data Source      | SportsDataIO API          |  
| Cloud Storage    | AWS S3                    |  
| Data Catalog     | AWS Glue                  |  
| Query Engine     | Amazon Athena             |  
| Execution        | CloudShell                |
| Automation       | Python, Boto3 SDK         |  
| Environment Mgmt | Python-dotenv             |  

## Project Structure  
```
nba-analytics-data-lake/  
├── src/  
│   ├── setup_nba_data_lake.py  # Infrastructure automation  
│   └── delete_resources.py     # Cleanup script  
├── .env                        # API credentials  
└── docs/                       # Architecture diagrams  
```


## Prerequisites  

1. **AWS Account** with permissions to:  
   - Create/delete S3 buckets  
   - Manage Glue databases  
   - Run Athena queries  
2. [SportsDataIO API Key](https://sportsdata.io/cart/free-trial) (Free Tier)  


## How to Setup

### Create IAM Policy
1. Log in to AWS Management Console

2. Navigate to IAM: 
- In the search bar, type IAM and select IAM from the results.

3. Create a New Policy:
- In the IAM dashboard, click on Policies in the left-hand menu.
- Click the Create policy button.
- Switch to JSON Editor:
- In the Create Policy page, select the JSON tab.
- Copy the [provided JSON policy](//policies/IAM%20Roles) and paste it into the editor.
 ![](/Assets//iam.png)

###  Launch CloudShell  
1. Sign into AWS Console → Click `>_` (CloudShell icon)  
 ![](/Assets//cloudshell.png)

### Configure Environment  

```bash
nano .env
``` 
1. Press `i` to insert  
2. Paste:  
```ini
SPORTS_DATA_API_KEY=your_actual_key_here
NBA_ENDPOINT=https://api.sportsdata.io/v3/nba/scores/json/Players
```
3. Save & exit.  

### Create Script File  
```bash
nano setup_nba_data_lake.py
```
1. Press `i` to enter insert mode  
2. Paste [script content](//src/setup_nba_data_lake.py)   
3. Save & exit.  


## Deployment 

### Install Dependencies
```bash
pip install -r requirements.txt
```
### Run Script 
```bash
python3 setup_nba_data_lake.py
```
**Successful Output**:  
```text
S3 Bucket Created: s3://<bucket_name>  
Glue Database 'nba_analytics' Ready  
Athena Query Interface Activated!  
```


## Query Demo  
Run in **Athena Query Editor**:  
```sql
-- Total points per team
SELECT Team, SUM(points) AS TotalTeamPoints 
FROM "glue-nba-data-lake"."nba_players" 
GROUP BY TEAM
ORDER BY TotalTeamPoints DESC 
LIMIT 5
```
![Athena Results](/Assets//athena-query-result.png)  

Run more queries & know about players or team.

## Validation  
1. **Verify S3 Data**:  
   - Navigate to S3 → Check `raw-data/nba_player_data.json` 
    ![](/Assets//bucket.png)
    ![](/Assets//bucket2.png) 
2. **Check Glue Catalog**:  
   - AWS Glue → Tables → `nba_players` schema  
    ![](/Assets//glue_table.png)


## Security Considerations  
- **IAM Roles**: Least privilege access for S3/Glue/Athena  
- **API Key Protection**: Stored in `.env` (not committed to Git)  
- **Encryption**: S3 server-side encryption enabled  


## Troubleshooting  
| Issue                          | Resolution                      |  
|--------------------------------|---------------------------------|  
| `BucketAlreadyExists`          | Use globally unique bucket name |  
| `AccessDenied` in Glue         | Verify IAM permissions          |  
| No data in Athena              | Wait 2-3 mins after Glue crawl  |  


## Future Enhancements  
1. Automated daily sync with EventBridge  
2. Data transformation to Parquet format  
3. Cost monitoring dashboard  

## Blog🔗
[To visit blog click here](https://blogs.vijaysingh.cloud/data-lake)

## Contributing  
1. Fork the repository  
2. Submit PRs to `new` branch  
 

## License  
MIT License - [Full Text](/LICENSE)  
