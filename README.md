# Asset-Visibility-and-Security-Hardening-with-Wazuh-Syscollector
## Objective
The purpose of this lab is to configure and utilize Wazuh's Syscollector module to gather a comprehensive system inventory of hardware and software assets on monitored endpoints. This inventory enhances visibility of resources within the IT infrastructure, helping to reduce the attack surface by identifying and documenting all hardware and software in use. Additionally, the lab covers querying the inventory for specific resources and generating reports to provide insight into endpoint configurations and supporting security.
## Lab Setup
The lab consists of two Virtual Machines (VMs). The Wazuh server is installed on an Ubuntu VM, and the agent is installed on a Windows VM. VMware is used as the virtualization platform.
## Skills Learned
- Endpoint System Inventory Management
- Generating and Interpreting System Inventory Reports
- System Inventory Querying and Analysis
## Tools Used
- Wazuh
- VMware
## System Inventory
A system inventory is a detailed record of hardware and software assets within an IT environment. Maintaining this inventory enables organizations to maximize visibility of their infrastructure and reduce the attack surface by identifying resources that may pose security risks. Wazuh’s SIEM capabilities assist IT teams in achieving this goal by collecting information such as hardware and OS details, installed software, network interfaces, ports, and running processes on each endpoint. The Wazuh agent creates a local database containing this data, which it then forwards to the Wazuh server for centralized management. The information collected depends on the agent configuration, and each endpoint has its unique system inventory.
## Configuring System Inventory Collection
The Syscollector module in Wazuh is responsible for gathering system information. To configure it, access the Wazuh agent configuration file located at `C:\Program Files (x86)\ossec-agent\ossec.conf` and navigate to the syscollector section which is all configuration between the `<woodle name="syscollector">`. You can use Ctrl+F to find the system inventory settings in Notepad.  

1. **Changing Frequency of System Scans**  
By default, system inventory scans are set to run every hour. However, this interval can be adjusted by modifying the `interval` tag to a different value. For example, setting `<interval>20m</interval>` will run scans every 20 minutes. To apply this change, edit the configuration file as described in the previous section.  

2. **Restart Wazuh Agent**  
  Restart Wazuh agent using command:
  ```xml
    Restart-Service -Name wazuh
  ```

3. **Result**

## Viewing System Inventory of an Agent
To view an agent's system inventory, navigate to `Options > Endpoint`. Click on the endpoint of interest. Click Inventory Data on the right of dashboard.  
**Result**  

## Querying the System Inventory Database
You can query the system inventory database to check for the presence of specific resources on an endpoint. This can be done using the Wazuh console, SQLite, or the curl command. For simplicity, this lab uses the Wazuh console. To access the console, navigate to `Server Management > Dev Tools` and enter a command such as `GET /syscollector/<AGENT_ID>/` to retrieve inventory data for a specific agent. Where Agent id is the unique id of the enrolled agent. The ids are usually given in order of which the agensts are enrolled. That is fisrt enrolled agent will have agent id 001, second agent will have agent id 002 and so on unless configured otherwise.

## Use Case: Querying System Inventory Database for Antivirus Software
An antivirus is essential for the average user. We can use the Wazuh console to verify the presence of antivirus software on the endpoint. Since antivirus programs often include terms like “antivirus”, “defender”, or “security” in their names, the following commands can be used to query the database.
  ```xml
    GET /syscollector/001/packages?pretty=true&search=antivirus
    GET /syscollector/001/packages?pretty=true&search=defender
    GET /syscollector/001/packages?pretty=true&search=security
  ```

## Another Example: Adding New Software and Checking Inventory
1. **Setting up environment**
   Install a new application (e.g., 7-Zip), the package should appear in the system inventory after the next scan interval (e.g., 20 minutes). To verify this, navigate to the system inventory dashboard and refresh the packages list.
   
2. **Searching for an Installed Package**  
   To locate the 7-Zip package, use the following command in the Wazuh console:
      ```xml
        GET /syscollector/001/packages?pretty=true&search=zip
      ```
        
3. **Result**
    
## Using System Inventory to View RAM Usage and Other Hardware Information
To view hardware information like RAM usage, CPU name, and core count, type the following command in the Wazuh console:
  ```xml
    GET /syscollector/001/hardware?pretty=true&select=ram.usage,cpu.name,cpu.cores
  ```

**Result**
## Generating System Inventory Reports
Wazuh can generate two types of inventory reports: a full report covering all resources or a report focused on a specific resource group (e.g., all installed software or hardware details). To generate a full report, navigate to `Options > Endpoint > [ENDPOINT_NAME] > Inventory Data` and click Generate Report. For a report on a specific resource type, navigate to `Options > Endpoint Summary > [ENDPOINT_NAME] > Inventory Data`, select the resource group of interest, and download the CSV file.
