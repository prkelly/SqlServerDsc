# Integration tests for SqlServerDsc

For it to be easier to write integration tests for a resource that depends on
other resources, this will list the run order of the integration tests that keep
their configuration on the AppVeyor build worker. For example, the integration
test for SqlAlwaysOnService enables and then disables the AlwaysOn functionality,
so that integration test are not listed.

If an integration test should use one or more of these previous integration test
configurations then the run order for the new integration tests should be set to
a higher run order number than the highest run order of the dependent integration
tests.

## SqlSetup

**Run order:** 1

The integration tests will install the following instances and leave them on the
AppVeyor build worker for other integration tests to use.

Instance | Feature | AS server mode
--- | --- | ---
DSCSQL2016 | SQLENGINE,AS,CONN,BC,SDK | MULTIDIMENSIONAL
DSCTABULAR | AS,CONN,BC,SDK | TABULAR
MSSQLSERVER | SQLENGINE,CONN,BC,SDK | -

All instances have a SQL Server Agent that is started.

### Properties for all instances

- **Collation:** Finnish\_Swedish\_CI\_AS
- **InstallSharedDir:** C:\Program Files\Microsoft SQL Server
- **InstallSharedWOWDir:** C:\Program Files (x86)\Microsoft SQL Server

### Users

The following local users are created on the AppVeyor build worker and can
be used by other integration tests.

> Note: User account names was kept to a maximum of 15 characters.

User | Password | Permission | Description
--- | --- | --- | ---
.\SqlInstall | P@ssw0rd1 | Local administrator. Administrator of Database Engine instance DSCSQL2016\*. | Runs Setup for the default instance.
.\SqlAdmin | P@ssw0rd1 | Administrator of all SQL Server instances. |
.\svc-SqlPrimary | yig-C^Equ3 | Local user. | Runs the SQL Server Agent service.
.\svc-SqlAgentPri | yig-C^Equ3 | Local user. | Runs the SQL Server Agent service.
.\svc-SqlSecondary | yig-C^Equ3 | Local user. | Used by other tests, but created here.
.\svc-SqlAgentSec | yig-C^Equ3 | Local user. | Used by other tests.

*\* This is due to that the integration tests runs the resource SqlAlwaysOnService
with this user and that means that this user must have permission to access the
properties `IsClustered` and `IsHadrEnable`.*

## SqlRS

**Run order:** 2

The integration tests will install the following instances and leave it on the
AppVeyor build worker for other integration tests to use.

Instance | Feature | Description
--- | --- | ---
DSCRS2016 | RS | The Reporting Services is left initialized, and in a working state.

### Properties for the instance

- **Collation:** Finnish\_Swedish\_CI\_AS
- **InstallSharedDir:** C:\Program Files\Microsoft SQL Server
- **InstallSharedWOWDir:** C:\Program Files (x86)\Microsoft SQL Server
- **DatabaseServerName:** `$env:COMPUTERNAME`
- **DatabaseInstanceName:** DSCSQL2016

## SqlDatabaseDefaultLocation

**Run order:** 2

The integration test will change the data, log and backup path of instance
**DSCSQL2016** to the following.

Data | Log | Backup
--- | --- | ---
C:\SQLData | C:\SQLLog | C:\Backups