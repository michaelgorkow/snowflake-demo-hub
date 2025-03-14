# Snowflake Demo Hub
A Streamlit application for discovering, installing, and managing Snowflake demos from GitHub repositories. Easily deploy and explore Snowflake demo projects with just a few clicks, streamlining the setup process for testing and learning new features.

```sql
USE ROLE ACCOUNTADMIN;

-- Create a demo role
CREATE OR REPLACE ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT ROLE SNOWFLAKE_DEMO_HUB TO USER ADMIN;

-- Create Git-API Integration
CREATE OR REPLACE API INTEGRATION GITHUB_INTEGRATION
    api_provider = git_https_api
    api_allowed_prefixes = ('https://github.com/')
    enabled = true
    comment='General Git-Integration';

-- Create a demo warehouses
CREATE WAREHOUSE IF NOT EXISTS WH_XSMALL WITH WAREHOUSE_SIZE='X-SMALL';
CREATE WAREHOUSE IF NOT EXISTS WH_SMALL WITH WAREHOUSE_SIZE='SMALL';
CREATE WAREHOUSE IF NOT EXISTS WH_MEDIUM WITH WAREHOUSE_SIZE='MEDIUM';
CREATE WAREHOUSE IF NOT EXISTS WH_LARGE WITH WAREHOUSE_SIZE='LARGE';

-- Grants to demo role
GRANT USAGE ON INTEGRATION GITHUB_INTEGRATION TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT CREATE DATABASE ON ACCOUNT TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT CREATE WAREHOUSE ON ACCOUNT TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT OPERATE, USAGE ON WAREHOUSE WH_XSMALL TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT OPERATE, USAGE ON WAREHOUSE WH_SMALL TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT OPERATE, USAGE ON WAREHOUSE WH_MEDIUM TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;
GRANT OPERATE, USAGE ON WAREHOUSE WH_LARGE TO ROLE SNOWFLAKE_DEMO_HUB_ROLE;

-- Create Installer App
USE ROLE SNOWFLAKE_DEMO_HUB_ROLE
CREATE DATABASE IF NOT EXISTS SNOWFLAKE_DEMO_HUB;
USE SCHEMA SNOWFLAKE_DEMO_HUB.PUBLIC;

CREATE GIT REPOSITORY GITHUB_REPO_SNOWFLAKE_DEMO_HUB
	ORIGIN = 'https://github.com/michaelgorkow/snowflake-demo-hub' 
	API_INTEGRATION = GITHUB_INTEGRATION 
	COMMENT = 'Github Repository from Michael Gorkow with Snowflake Demo Hub.';

-- Run the installation of the Streamlit App
EXECUTE IMMEDIATE FROM @SNOWFLAKE_DEMO_HUB.PUBLIC.GITHUB_REPO_SNOWFLAKE_DEMO_HUB/branches/main/setup.sql;
```