---
id: qnqlswg6d1tf3awe47ivrjn
title: principles
desc: ''
updated: 1710088921249
created: 1710086838882
---


#### Overall Engineering Philosophy
- The goal is to build high performing easily maintained processes with six nines of reliability. 
- The enterprise data warehouse HAS to be right and it has to be highly available, otherwise it will not be used. 
    - People will go back to their department specific reports built on transactional systems which slows the transactional system down which in turn makes users unhappy.
- You build data warehouse ETL to tight tolerances to ensure that everybody maintains faith that the system is popping out reliable, accurate, actionable data in a timely fashion. You get data to the right people, at the right place, at the right time.
- While it may never be perfect, as long as you strive for perfection and never back down from people trying to water down your processes, you will be able to build a system close enough to perfect that it is indistinguishable from an actual perfect system.
- Data warehouse ELT should hum along in the background like a utility that is fundamental to our daily lives but rarely noticed because of its ubiquity and reliability. If you do things right, people will not be certain you did anything at all. 

#### Data Flow Design: Keep it simple
- Data flows into the data warehouse though ODS and ODS only. ODS is the front door to the system and this should always remain true to ensure ease of system maintenance.
- Agent jobs that manage the warehouse load are named in order of execution. This pattern should also never drift far from its initial state. You can insert extra items, but the ordinal relationships need to be set in stone. As an example, fact tables should never process before dimension tables.
- All the steps in the warehouse load are loosely coupled meaning you can run all the steps independently with the proviso that that prior step ran at some point. In other words, if one step blows up, you do not have to go back and run the entire process. 
- Data flows into the data warehouse in the following manner. In all cases, you are only processing data flagged as cleaned, unprocessed, and error free.
  1. Data is pulled from source systems into a specific staging table in ODS.
  2. The data is cleaned.
  3. Data from your systems EDW dimensions are pulled into the common model in ODS.
  4. Existing dimensional data is compared to incoming dimensional data and actions are taken based upon the rules surrounding a particular dimension and loaded into Production.
  5. Dimensional fields used to identify records are flushed back to ODS for matching with facts.
  6. Fact records are matched up with their dimensions and loaded into the common model.
  7. The common model is flushed to production.

#### Code First Philosophy

- Writing code to solve ETL problems will represent a signification cost reduction to the organization's IT spend. 

#### Master Data Management
- Do MDM first! MDM is fundamental to a properly functioning EDW. 
- This is the collection of processes necessary to manage your master data. Your master data is data that might be sourced from different systems but is ultimately the same entity. Customers are a good example of a master data entity. 
- Ideally, master data management is something that should be set up on day one. The sooner you deal with this issue, the cheaper the solution will be.

#### Open Architecture
- The framework is open everything especially open architecture. 
- It is designed to so that you can easily integrate new data sources and new data models rapidly with a minimum of modification to existing processes.

#### Decoupled Systems
- The formal term for decoupled systems is separation of concerns. 
- Monolithic processes are a bad idea
    - If one thing breaks, the whole thing breaks.
    - If you have to fix one thing, you have to redeploy the entire thing.
    - Finding the broken thing in the first place can be a challenge.
    - Having more than one person work on the load can be a challenge even with modern source control.
- The framework breaks things down based on a Unix philosophy of many small programs working together.
    - Each process is isolated and executes separately from other processes. 
    - Each process is executed by a single stored procedure. That stored procedure does one thing and one thing only. It also does not depend on another process executing properly. 
    - If a previous step does not run, then the proc will still run just nothing happens if there is nothing to process. This is important in the case where a previous step does not run, and yet there is still data to process. You will find this handy during error recovery procedures.
- Decoupled systems is one of the highest value aspects of the framework. 
    - It reduces maintenance and speeds up error recovery
    - Speeds up development time for new processes

#### Independent Identically Executing Processes
- IIE processes are:
    - decoupled and executed in isolation from other processes
    - execute exactly the same as the same class of object
- The framework provides a pattern for each step of the data flow. 
- Within that pattern is a standardized method of processing data. All pulls work exactly the same. All dimension loads work exactly the same etc.

#### Robust Processes
- The framework is designed to take a beating and keep ticking. 
    - There is a full suite of error detection and recovery processes. 
    - There is a standard retry process if a step blows up. 
    - If a step blows up, just go back and re-run that step without any kind of modification to code or having a separate recovery process.

#### Self-Diagnostics
- It is not humanly possible to keep an eye on everything, so the framework comes with solutions for the process to monitor itself. 
- The outputs of this software can be used to help diagnose and correct issues.
- Basically, when things go sideways, the software will tell you what’s wrong so you can fix it before anybody even notices.

#### Data Provenance
- Finding out where data came from is a key element of the framework as it assists in error recovery. 
- It goes beyond just having columns in tables that indicate source systems. 
- The provenance of data is tracked from the source system all the way to EDW and there are several checks that are part of the monitoring process to ensure that all the data got from where it is going, to where it is supposed to go.

#### Adverse Reporting
- The ETL Framework only reports adverse events. 
- the assumption is that the warehouse successfully processes everyday. 
- This assumption does two things:
    - Significantly reduces email traffic so EDW stakeholders do not get numb to daily emails.
    - Makes it easier to spot emails that are reporting errors.
- While some executives may want daily completion emails, we strongly urge you to resist this request, or develop a system by where the people that are responsible for the care and feeding of the data warehouse are not getting daily completion emails.

#### Error Recovery
- Error recovery is designed to be as easy as possible. In many cases, it can be as simple as flipping a flag and rerunning the process.
- Even in sophisticated error scenarios, after you have resolved the issue, getting the database back up and running by running unprocessed data without having to do a bunch of modifications to code or some special process. This is also a deliberate design principal of the framework even though it naturally shakes out from other concepts like decoupled systems and IIE processes.

#### Archrival And Recovery
- In the scenario where the source of data is a database, pull processes are designed to check what you have vs. what exist in the source system. This allows for recovery of data by simply blowing away what you do not want in EDW and rerunning the pull process.
- In the scenario where the source of data is a file, the framework provides a process to archive that file. During error recovery, the process of retrieving that file and loading the data warehouse is not as smooth as I would like. This is a scenario where an extra process is required because retrieving data from archived files is more complex than just yanking history back from a source system.
- Even yanking data back from a source system may not be an option as many transactional systems do not even house historical data.
