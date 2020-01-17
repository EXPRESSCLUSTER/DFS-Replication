# Use Windows DFS Replication with EXPRESSCLUSTER (2 nodes cluster)

## About This Guide

This guide provides how to DFS Replication with EXPRESSCLUSTER X (ECX) with 2 nodes. The guide assumes that its readers have EXPRESSCLUSTER X basic knowledge and setup skills.

Note: DFS Replication replicates data between servers, but the data is accessible even on standby server. We assume that client accesses the data via a floating IP address. By following this manner of data access, we ensure the data exclusivity.

## System Overview

### System Requirement

- 2 servers are required.
    - OS is Windows.
    - Both servers are members of a same domain.
    - Both servers are IP reachable each other.

### System Configuration

- Windows Server 2016 Standard Edition
    - Install DFS Replication
    - Install EXPRESSCLUSTER X for Windows
    	- Ping NP resolution resource
        - Failover Group
            - Floating IP address resource
            - Some kind of resource for starting and stopping a database

        - Monitor resource
            - User-mode monitor resource
            - Floating IP monitor resource
            - Some kind of resource for monitoring a database

- Sample configuration
    ```
		<LAN or Internet>
		 |
		 |  +----------------------------+
		 +--| Primary Server             |
		 |  | - Windows Server 2016      |
		 |  | - EXPRESSCLUSTER X 4.1     |
         	 |  | - DFS Replication          |
		 |  |                            |
		 |  | RAM   : 4GB                |
		 |  | Disk 0: 40GB for OS        |
		 |  |      C: Local partition    |
		 |  | Disk 1: 100GB for database |
		 |  |      E: Striped volume     |
		 |  +----------------------------+
		 |
		 |  +----------------------------+
		 +--| Secondary Server           |
		 |  | - Windows Server 2016      |
		 |  | - EXPRESSCLUSTER X 4.1     |
         	 |  | - DFS Replication          |         
		 |  |                            |
		 |  | RAM   : 4GB                |
		 |  | Disk 0: 40GB for OS        |
		 |  |      C: Local partition    |
		 |  | Disk 1: 100GB for database |
		 |  |      E: Striped volume     |
		 |  +----------------------------+
		 |
    ```

## How to setup

### Create a striped volume (If you need)

Combine 2 disks with one striped volume on **Disk Management**.

This striped volume is used for the database, and a folder in this volume is replicated between 2 servers by DFS Replication.

### Install DFS Replication on Server Manager

Install following roles and features

- File and Storage Services
	- File and iSCSI Services
		- DFS Replication
		- File Server
- Remote Server Administration Tools
	- Role Administration Tools
	    - DFS Management Tools
			
			
### Create a New Replication Group on primary server

1. Open **DFS Management**
2. Right click **Replication** and select **New Replication Group...**
3. **Replication Group Type**: Select **Multipurpose replication group**
4. **Replication Group Memebers**: Add 2 servers
5. **Topology Selection**: Select **Full mesh**
6. **Replication Group Schedule and Bandwith**: No need to change
7. ** Primary Member**: Select primary server
8. **Folders to Replicate**: Select the folder on the striped disk.
9. **Local Path of test on Other Members**: Select **Enabled** and the same folder on **Edit** page
	
### Install EXPRESSCLUSTER

1. Install EXPRESSCLUSTER X
2. Register ECX licenses
    - EXPRESSCLUSTER X for Windows
3. Create a cluster
    - Ping NP resolution resource
    - Failover Group: failover
        - fip: floating IP resource
4. Start a group on primary server

### Install database application on both nodes

### Add resources for starting and stopping database to ECX cluster

### Add monitor resources for monitoring database to ECX cluster

