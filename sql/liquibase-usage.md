# Using Liquibase

[Liquibase](https://www.liquibase.org/) is a database deployment tool that we considered using at work.

These are the steps I used to get up and testing. Kinda long...

## Setup

I used the default installer on my local machine, but created a different directory to hold code, 'liquibase-demo'.

(Liquibase also maintains an image on [dockerhub](https://hub.docker.com/r/liquibase/liquibase), which we would use in a production environment.)

A 'liquibase.properties' file was created in this directory, and my changes look like:

```bash
#### Enter the Target database 'url' information  ####
liquibase.command.url=jdbc:sqlserver://<SERVER_FQDN>:1433;instanceName=<INSTANCE_NAME>;databaseName=Liquibase_NEW;encrypt=true;trustServerCertificate=true;

# Enter the username for your Target database.
liquibase.command.username: liquibase_test

# Enter the password for your Target database.
liquibase.command.password: <password>

# Enter URL for the source database
liquibase.command.referenceUrl: jdbc:sqlserver://<SERVER_FQDN>:1433;instanceName=<INSTANCE_NAME>;databaseName=Liquibase_PROD;encrypt=true;trustServerCertificate=true;

# Enter the username for your source database
liquibase.command.referenceUsername: liquibase_test

# Enter the password for your source database
liquibase.command.referencePassword: <password>

liquibase.pro.licensekey: <license_key>
```

I created a database called 'Liquibase_NEW' on a dev instance, created all of the default schemas from script, and made sure the test user had access.  
(I ended up having to grant the 'liquibase_test' login sa permissions in order to get through the testing. I'm sure this could be fine tuned.)

## Baseline run

From a copy of our "Golden" database, I used the 'Generate Scripts' function in SSMS to select a few objects and create a baseline script. This script was saved in a 'baseline' directory, and named with the current release version.

The first xml changelog is named 'root.changelog.xml', and its contents are:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:ext="http://www.liquibase.org/xml/ns/dbchangelog-ext"
    xmlns:pro="http://www.liquibase.org/xml/ns/pro"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.9.xsd
        http://www.liquibase.org/xml/ns/dbchangelog-ext http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-ext.xsd
        http://www.liquibase.org/xml/ns/pro http://www.liquibase.org/xml/ns/pro/liquibase-pro-4.9.xsd">
    <changeSet id="2022.01.0.911" author="shane.sutton@company.com">
        <sqlFile path="baseline/2022.01.0.911.sql" />
    </changeSet>
</databaseChangeLog>
```

Note that it provides metadata and then points to the baseline SQL script.

From the Liquibase directory that I created, the command to run the changelog is:

```bash
PS C:\git\liquibase-demo> liquibase --changelog-file=root.changelog.xml update
...
Running Changeset: root.changelog.xml::2022.01.0.911::shane.sutton@company.com
Liquibase command 'update' was executed successfully.
```

Afterwards all of the objects in the script exist in the database. Also, Liquibase creates two tracking tables: 'dbo.DATABASECHANGELOG' and 'dbo.DATABASECHANGELOGLOCK'

The contents of the log after the initial run:

| ID            | AUTHOR                     | FILENAME           | DATEEXECUTED            | ORDEREXECUTED | EXECTYPE | MD5SUM                             | DESCRIPTION | COMMENTS | TAG    | LIQUIBASE | CONTEXTS | LABELS | DEPLOYMENT_ID |
| -----------   | -------------------------- | ------------------ | ----------------------- | ------------- | -------- | ---------------------------------- | ----------- | -------- | ------ | --------- | -------- | ------ | ------------- |
| 2022.01.0.911 | shane.sutton@company.com | root.changelog.xml | 2022-05-23 16:01:27.370 | 1             | EXECUTED | 8:c23a438508b93c8764e9d964f94f4ad0 | sqlFile     |          | NULL   | 4.10.0    | NULL     | NULL   | 3321021418    |

## Subsequent runs

For following releases, I created a 'sql' directory, and then in that created a new folder for each release. The release folders contain our typical object specific folders: '050_CreateTable', '150_Views', etc

Inside of each object folder are nothing but our typical raw SQL files. There is no Liquibase specific formatting, although this could be an option.

There appear to be different methods of specifying metadata to Liquibase for each `liquibase update`. What I've done for now is to create a release specific xml changelog file in the 'sql' directory, at the same level as the release folders.  
This is likely not how we'd handle it if we start using the product...

Then, to add the next release to a deployment you just modify the root xml file, adding a pointer to the release specific xml file.

For an example '2022.02.0.1000' release, the following line would be added to 'root.changelog.xml', after the initial `</changeset>`:

```xml
    <include file = "sql/2022.02.0.1000.changelog.xml"  relativeToChangelogFile="true" />
```

The new changelog, '2022.02.0.1000.changelog.xml':

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:ext="http://www.liquibase.org/xml/ns/dbchangelog-ext"
    xmlns:pro="http://www.liquibase.org/xml/ns/pro"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.9.xsd
        http://www.liquibase.org/xml/ns/dbchangelog-ext http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-ext.xsd
        http://www.liquibase.org/xml/ns/pro http://www.liquibase.org/xml/ns/pro/liquibase-pro-4.9.xsd">
    <changeSet id="2022.02.0.1000" author="shane.sutton@company.com"/>
    <includeAll path="2022.02.0.1000/" relativeToChangelogFile="true"/>
</databaseChangeLog>
```

Using this approach you can start an update with the same command:

```bash
PS C:\git\liquibase-demo> liquibase --changelog-file=root.changelog.xml update

Running Changeset: sql/2022.02.0.1000.changelog.xml::2022.02.0.1000::shane.sutton@company.com
Running Changeset: sql/2022.02.0.1000/050_CreateTable/DataLoad.OffPrimaryDisplayStaging.sql::raw::includeAll
Running Changeset: sql/2022.02.0.1000/050_CreateTable/Dataload.InsertORPurgeListAudit.sql::raw::includeAll
Running Changeset: sql/2022.02.0.1000/050_CreateTable/Markdown.MarkdownHistory.sql::raw::includeAll
Running Changeset: sql/2022.02.0.1000/060_CreateIndex/CreateIndex_Markdown.MarkdownHistory.sql::raw::includeAll
Running Changeset: sql/2022.02.0.1000/110_AlterData/3507_PLAT-1343_AlterData_App.Culture.sql::raw::includeAll
Running Changeset: sql/2022.02.0.1000/150_Views/v_General_DatabaseParameter.sql::raw::includeAll
Liquibase command 'update' was executed successfully.
```

It detects the information in the `<include>` tag to find the new changelog.  
The new changelog provides metadata with `<changeSet>` and the location of the SQL files with `<includeAll>`.

The new results in 'dbo.DATABASECHANGELOG' (several columns omitted):

| ID             | AUTHOR                     | FILENAME                                                                    | ORDEREXECUTED | EXECTYPE | DESCRIPTION | DEPLOYMENT_ID |
| -------------- | -------------------------- | --------------------------------------------------------------------------- | ------------- | -------- | ----------- | ------------- |
| 2022.02.0.1000 | shane.sutton@company.com | sql/2022.02.0.1000.changelog.xml                                            | 2             | EXECUTED | empty       | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/050_CreateTable/DataLoad.OffPrimaryDisplayStaging.sql    | 3             | EXECUTED | sql         | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/050_CreateTable/Dataload.InsertORPurgeListAudit.sql      | 4             | EXECUTED | sql         | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/050_CreateTable/Markdown.MarkdownHistory.sql             | 5             | EXECUTED | sql         | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/060_CreateIndex/CreateIndex_Markdown.MarkdownHistory.sql | 6             | EXECUTED | sql         | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/110_AlterData/3507_PLAT-1343_AlterData_App.Culture.sql   | 7             | EXECUTED | sql         | 3324543497    |
| raw            | includeAll                 | sql/2022.02.0.1000/150_Views/v_General_DatabaseParameter.sql                | 8             | EXECUTED | sql         | 3324543497    |

All new rows share the same 'DEPLOYMENT_ID', but the `includeAll` flag causes it to lose specific metadata for each file.

## Liquibase formatted SQL

Each SQL file can also contain Liquibase specific metadata in opening comments.

For example, for a test '2022.03.0.3000' deployment, I added two scripts to create dummy tables. The following was added to the top of each:

```sql Script 1
-- liquibase formatted sql
-- changeset shane.sutton@company.com:2022.03.0.3000
```

```sql Script 2
-- liquibase formatted sql
-- changeset john.doe@company.com:2022.03.0.3000
```

The `changeset` comment specifies the author of the script, followed by a `:`, followed by the script ID.
Note that the same ID can be used in each file, AND in the release XML. This gives us some options for templating the file, and then auto-populating the ID to each script.

Add the following to 'root.changelog.xml', after the first `<include>` (for 2022.02.0.1000):

```xml
    <include file = "sql/2022.03.0.3000.changelog.xml"  relativeToChangelogFile="true" />
```

And the relevant pieces of the new changelog, '2022.03.0.3000.changelog.xml:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    ...
    <changeSet id="2022.03.0.3000"  author="NewDeploymentTool"/>
    <includeAll path="2022.03.0.3000/" relativeToChangelogFile="true"/>
</databaseChangeLog>
```

The update and output:

```bash
PS C:\git\liquibase-demo> liquibase --changelog-file=root.changelog.xml update

Running Changeset: sql/2022.03.0.3000.changelog.xml::2022.03.0.3000::NewDeploymentTool
Running Changeset: sql/2022.03.0.3000/050_CreateTable/General.DummyTable.sql::2022.03.0.3000::shane.sutton@company.com
Running Changeset: sql/2022.03.0.3000/050_CreateTable/General.MoreDummy.sql::2022.03.0.3000::john.doe@company.com
Liquibase command 'update' was executed successfully.
```

And the new rows in 'dbo.DATABASECHANGELOG', with a previous row included for reference:

| ID             | AUTHOR                     | FILENAME                                                     | ORDEREXECUTED | EXECTYPE | DESCRIPTION | DEPLOYMENT_ID |
| -------------- | -------------------------- | ------------------------------------------------------------ | ------------- | -------- | ----------- | ------------- |
| raw            | includeAll                 | sql/2022.02.0.1000/150_Views/v_General_DatabaseParameter.sql | 8             | EXECUTED | sql         | 3324543497    |
| 2022.03.0.3000 | NewDeploymentTool          | sql/2022.03.0.3000.changelog.xml                             | 9             | EXECUTED | empty       | 3326372210    |
| 2022.03.0.3000 | shane.sutton@company.com | sql/2022.03.0.3000/050_CreateTable/General.DummyTable.sql    | 10            | EXECUTED | sql         | 3326372210    |
| 2022.03.0.3000 | john.doe@company.com     | sql/2022.03.0.3000/050_CreateTable/General.MoreDummy.sql     | 11            | EXECUTED | sql         | 3326372210    |

Now that each SQL script contains metadata, the release and different authors can be specified for each file, with all rows sharing the same DEPLOYMENT_ID.

## Running a diff

When running a `liquibase diff`, be sure to specify all of the schemas you want to compare. By default it only includes 'dbo'.

To prepare, I will modify the root changelog to only include the baseline in order to deploy to an empty 'Liquibase_DEV'.  
I've also modified the 'liquibase.properties' file, making 'Liquibase_DEV' the target and 'Liquibase_NEW' the source database.

Run a baseline deployment on 'Liquibase_DEV':

```bash
PS C:\git\liquibase-demo> liquibase --changelog-file=root.changelog.xml update

Running Changeset: root.changelog.xml::2022.01.0.911::shane.sutton@company.com
Liquibase command 'update' was executed successfully.
```

Run a diff, specifying schema (long output):

```bash
PS C:\git\liquibase-demo> liquibase diff --schemas="dbo,<schema_list_omitted>"

Diff Results:
Reference Database: liquibase_test @ jdbc:sqlserver://<SERVER_FQDN>\<INSTANCE_NAME>:1433;connectRetryInterval=10;connectRetryCount=1;maxResultBuffer=-1;sendTemporalDataTypesAsStringForBulkCopy=true;delayLoadingLobs=true;useFmtOnly=false;useBulkCopyForBatchInsert=false;cancelQueryTimeout=-1;sslProtocol=TLS;jaasConfigurationName=SQLJDBCDriver;statementPoolingCacheSize=0;serverPreparedStatementDiscardThreshold=10;enablePrepareOnFirstPreparedStatementCall=false;fips=false;socketTimeout=0;authentication=NotSpecified;authenticationScheme=nativeAuthentication;xopenStates=false;sendTimeAsDatetime=true;replication=false;trustStoreType=JKS;trustServerCertificate=true;TransparentNetworkIPResolution=true;serverNameAsACE=false;sendStringParametersAsUnicode=true;selectMethod=direct;responseBuffering=adaptive;queryTimeout=-1;packetSize=8000;multiSubnetFailover=false;loginTimeout=15;lockTimeout=-1;lastUpdateCount=true;encrypt=true;disableStatementPooling=true;databaseName=Liquibase_NEW;columnEncryptionSetting=Disabled;applicationName=Microsoft JDBC Driver for SQL Server;applicationIntent=readwrite; (Default Schema: dbo)
Comparison Database: liquibase_test @ jdbc:sqlserver://<SERVER_FQDN>\<INSTANCE_NAME>:1433;connectRetryInterval=10;connectRetryCount=1;maxResultBuffer=-1;sendTemporalDataTypesAsStringForBulkCopy=true;delayLoadingLobs=true;useFmtOnly=false;useBulkCopyForBatchInsert=false;cancelQueryTimeout=-1;sslProtocol=TLS;jaasConfigurationName=SQLJDBCDriver;statementPoolingCacheSize=0;serverPreparedStatementDiscardThreshold=10;enablePrepareOnFirstPreparedStatementCall=false;fips=false;socketTimeout=0;authentication=NotSpecified;authenticationScheme=nativeAuthentication;xopenStates=false;sendTimeAsDatetime=true;replication=false;trustStoreType=JKS;trustServerCertificate=true;TransparentNetworkIPResolution=true;serverNameAsACE=false;sendStringParametersAsUnicode=true;selectMethod=direct;responseBuffering=adaptive;queryTimeout=-1;packetSize=8000;multiSubnetFailover=false;loginTimeout=15;lockTimeout=-1;lastUpdateCount=true;encrypt=true;disableStatementPooling=true;databaseName=Liquibase_DEV;columnEncryptionSetting=Disabled;applicationName=Microsoft JDBC Driver for SQL Server;applicationIntent=readwrite; (Default Schema: dbo)
Compared Schemas: <SCHEMA_LIST_OMITTED>, dbo
Product Name: EQUAL
Product Version: EQUAL
Missing Catalog(s): NONE
Unexpected Catalog(s): NONE
Changed Catalog(s):
     Liquibase_NEW
          name changed from 'Liquibase_NEW' to 'Liquibase_DEV'
Missing Check Constraint(s): NONE
Unexpected Check Constraint(s): NONE
Changed Check Constraint(s): NONE
Missing Column(s):
  SCHEMA: DataLoad
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.ProductOffPrimaryDisplayDate.<column_hidden>
     DataLoad.ProductOffPrimaryDisplayDate.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
     DataLoad.InsertORPurgeListAudit.<column_hidden>
  SCHEMA: General
     General.v_DatabaseParameter.DatabaseParameterId
     General.v_DatabaseParameter.DatabaseParameterValue
     General.DummyTable.OffPrimaryDisplayDate
     General.MoreDummy.OffPrimaryDisplayDate
     General.DummyTable.ProductId
     General.MoreDummy.ProductId
  SCHEMA: Markdown
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
     Markdown.MarkdownHistory.<column_hidden>
Unexpected Column(s): NONE
Changed Column(s): NONE
Missing Database Package(s): NONE
Unexpected Database Package(s): NONE
Changed Database Package(s): NONE
Missing Database Package Body(s): NONE
Unexpected Database Package Body(s): NONE
Changed Database Package Body(s): NONE
Missing Foreign Key(s): NONE
Unexpected Foreign Key(s): NONE
Changed Foreign Key(s): NONE
Missing Function(s): NONE
Unexpected Function(s): NONE
Changed Function(s): NONE
Missing Index(s):
  SCHEMA: DataLoad
     PK_ProductOffPrimaryDisplayDate UNIQUE  ON DataLoad.ProductOffPrimaryDisplayDate(ProductId)
  SCHEMA: General
     PK_DummyTable UNIQUE  ON General.DummyTable(ProductId)
     PK_MoreDummy UNIQUE  ON General.MoreDummy(ProductId)
  SCHEMA: Markdown
     PK_MarkdownMarkdownHistory_ProductId_StoreId_OptimizeStartDate UNIQUE  ON Markdown.MarkdownHistory(ProductId, StoreId, OptimizeStartDate)
     UCX_DummyIndex ON Markdown.MarkdownHistory(StoreId, MarkdownNumber)
Unexpected Index(s): NONE
Changed Index(s): NONE
Missing Primary Key(s):
  SCHEMA: DataLoad
     PK_ProductOffPrimaryDisplayDate on DataLoad.ProductOffPrimaryDisplayDate(ProductId)
  SCHEMA: General
     PK_DummyTable on General.DummyTable(ProductId)
     PK_MoreDummy on General.MoreDummy(ProductId)
  SCHEMA: Markdown
     PK_MarkdownMarkdownHistory_ProductId_StoreId_OptimizeStartDate on Markdown.MarkdownHistory(ProductId, StoreId, OptimizeStartDate)
Unexpected Primary Key(s): NONE
Changed Primary Key(s): NONE
Missing Schema(s): NONE
Unexpected Schema(s): NONE
Changed Schema(s): NONE
Missing Sequence(s): NONE
Unexpected Sequence(s): NONE
Changed Sequence(s): NONE
Missing Stored Procedure(s): NONE
Unexpected Stored Procedure(s): NONE
Changed Stored Procedure(s): NONE
Missing Synonym(s): NONE
Unexpected Synonym(s): NONE
Changed Synonym(s): NONE
Missing Table(s):
  SCHEMA: DataLoad
     InsertORPurgeListAudit
     ProductOffPrimaryDisplayDate
  SCHEMA: General
     DummyTable
     MoreDummy
  SCHEMA: Markdown
     MarkdownHistory
Unexpected Table(s): NONE
Changed Table(s): NONE
Missing Trigger(s): NONE
Unexpected Trigger(s): NONE
Changed Trigger(s): NONE
Missing Unique Constraint(s): NONE
Unexpected Unique Constraint(s): NONE
Changed Unique Constraint(s): NONE
Missing View(s):
  SCHEMA: General
     v_DatabaseParameter
Unexpected View(s): NONE
Changed View(s): NONE
Liquibase command 'diff' was executed successfully.
```

I don't know how useful this would be for automation. There is a way to generate a changelog from a diff, but it would have to be reviewed.

Liquibase suggests some best practices for diffs: [https://www.liquibase.com/blog/liquibase-diffs](https://www.liquibase.com/blog/liquibase-diffs)

In that post, it sums it up "Database migrations are the best way forward", suggesting that they only recommend diffs for certain tasks.
