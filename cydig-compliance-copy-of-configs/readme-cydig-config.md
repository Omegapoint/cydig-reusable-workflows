# CyDig Configuration

Before scanning your repositories, you must add the configuration file. The values in this configuration file are the <ins>default values</ins>, which means they will be used for repository scans unless you specify other values to override when calling the `compliance-template.yml`.

The configuration file to be used for repository scan is:

* [cydigConfig.json](https://github.com/Omegapoint/cydig-compliance/blob/main/github/cydigConfig.json) 

## Setting Up CyDig Configuration

The configuration file, `cydigConfig.json`, contains placeholder values that are not accepted in the scans. 
To configure CyDig for your repository or project, follow these steps:

1. Open the `cydigConfig.json` file.
2. Specify appropriate values for each variable in the configuration file. If a value is not applicable, mark it as <ins>"not specified"</ins>.

Here's a brief description of each variable in CyDig Configuration:

### **teamName** 

- Description: Name of your team, delivery, or customer.
- Usage: Consult with the CyDig team for the appropriate value. All repositories will use the same team name, meaning you cannot override this value from the workflow.
   ```yaml
   "teamName": "name-of-your-team"
   ```

### **usingAzure** 

- Description: Set to true if you are using Azure, false otherwise.
- Usage: If set to true, ensure you have added a service connection to access the correct Azure subscription.
    ```yaml
    "usingAzure": true/false
    ```
### **threatModeling**
- Description: Date of performed threat modeling (for all repositories) and a special tag for threat modeling tickets in Azure DevOps Boards.
- Usage: If applicable, specify the date or tag. If date is not applicable, set as: ```"date": "not specified" ```. Default for tag is "TM" and does not need to be changed if not using Azure DevOps Boards.
    ```yaml
    "threatModeling": {
        "date": "date-of-threat-modeling",
        "boardsTag": "TM" 
    }
    ```
### **pentest** 
- Description: Same as for threatModeling but with the tag "PT".
- Usage: If applicable, specify the date or tag. If date is not applicable, set as: ```"date": "not specified" ```. Default for tag is "PT."
    ```yaml
    "pentest": {
        "date": "date-of-pentest",
        "boardsTag": "PT"
    }
    ```
### **github**
- Description: Specify if you are using repositories and boards in GitHub.
- Usage: Fill in the "repos" or "boards" section.

    - *If you are using repositories:*
    **username**: Name of the user whose access token you are using. The access token should be added to the template for scanning exposed secrets.
    - *If you are using boards:*
    **nameOfBoards**: Name of your boards in your GitHub Project. Use ```"nameOfBoards": "not specified" ``` for all boards or list them as ```"nameOfBoards": "board 1, board 2, board 3" ```

    ```yaml
    "github": {
        "usingRepos": true/false,
        "repos": {
            "username": "firstname.lastname (usually)"
        },
        "usingBoards": true/false,
        "boards": {
            "nameOfBoard": "name-of-boards (use 'not specified' for all boards in project)"
        }
    }
    ```

### **scaTool**
- Description: The selection of a Software Composition Analysis (SCA) tool.
- Usage: Fill in the name of the SCA tool or use ```"nameOfTool": "not specified", ``` if not using any.
    - If using OWASP Dependency Check, specify the path to the generated report file. 
    - `csvPath` is required for central scans but not for repository scans. Default is ```"csvPath": "not specified"```.

    - If you are using **OWASP Dependency Check** as SCA tool, and have all generated reports in a folder on the runner, and an aggregated csv-report of vulnerabilities in all repositories with on the following columns (separated by a ; ):

        1. Project - name of repository
        2. CVSSv3_BaseSeverity - (low, medium, high, critical or empty)
        3. CVSSv2_Severity - (low, medium, high or empty)

        Then you can use the control by sending in the following parameter to get number of vulnerabilities of each severity in each repository.
    - If you are specifying a SCA tool that is not supported by CyDig, you will only see the name of the tool without details about the number of vulnerabilities identified.
    ```yaml
    "scaTool": {
        "nameOfTool": "name-of-tool", 
        "owaspDependencyCheck": {
            "reportPath": "Reports/dependency-check-report.csv",
            "csvPath": "not specified"
        }
    }
    ```
### **sastTool**
- Description: The selection of a Static Application Security Testing (SAST) tool.
- Usage: Fill in the name of the SAST tool or use ```"nameOfTool": "not specified"``` if not using any.

    - If using Semgrep, specify the path to the generated report file.
    - If you are specifying a SAST tool that is not supported by CyDig, you will only see the name of the tool without details about the number of vulnerabilities identified.

    ```yaml
    "sastTool": {
        "nameOfTool": "name-of-tool",
        "semgrep": {
            "reportPath": "semgrep-json.json"
        }
    }
    ```


### **codeQuality**
- Description: The selection of a Code Quality tool.
- Usage: Fill in the name of the Code Quality tool or use ```"nameOfTool": "not specified"``` if not using any.

    - If you are specifying a SCA tool that is not supported by CyDig, you will only see the name of the tool without details about the number of vulnerabilities identified.

    ```yaml
    "codeQualityTool": {
        "nameOfTool": "name-of-tool"
    }
    ```