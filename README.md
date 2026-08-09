# RDCMan

Download latest version from Releases:       
https://github.com/rdclyn/RDCMan/releases/tag/v3.12

## Introduction

RDCMan is a Windows utility for administering multiple Remote Desktop sessions from a single workspace. It is intended for IT specialists who repeatedly connect to server farms, lab machines, administrative workstations, and virtualized environments. Systems are stored in RDG files and arranged as a hierarchy of groups and server entries, allowing connection properties to be managed centrally instead of repeated for every host.

A server entry identifies a network name or IP address and can also have a separate display name. Groups can inherit configuration from parent containers or application defaults, so credentials, gateway parameters, display behavior, and other settings can be defined once and reused. RDCMan also supports credential profiles, useful when the same account must be referenced by unrelated groups without duplicating authentication data.

The application combines a server tree with an embedded RDP client area. Selecting a server displays its remote desktop, while selecting a group can show member systems as thumbnails, including live session previews when enabled. Virtual groups provide working views for connected, recently used, favorite, temporary, or reconnecting systems.

RDCMan is particularly effective when the hierarchy mirrors administrative boundaries. Separate files or groups for production, testing, customer environments, and privileged infrastructure reduce navigation mistakes and make inherited settings easier to audit. Because many connection properties are evaluated when a session starts, administrators should plan changes around reconnect operations rather than assuming that an active session will immediately adopt modified settings.

## Organizing Servers and Configuration

The RDG file is the top-level configuration container in RDCMan. Servers must belong to groups, and groups must belong to a file. Nested groups are supported, but a group is homogeneous: it contains either child groups or server entries, not both. A practical structure is a file containing regional groups, each with role groups that contain the actual servers.

Inheritance reduces duplicated configuration. Properties marked to inherit from the parent are resolved through the hierarchy until RDCMan reaches an explicitly configured value or the default group settings. Place gateway and display settings on a site group, credentials on a narrower administrative group, and host-specific exceptions on individual server entries. Most server-related changes take effect on the next connection, so reconnect when validating modified resolution, experience, or connection options.

Large inventories can be entered with expansion patterns. `web{a,b,c}` creates three names, while `sql[001-015]` expands a zero-padded numeric range. Patterns can be combined for structured naming schemes. Servers can also be imported from a text file containing one host name per line. If an imported name already exists, its preferences are updated rather than duplicated.

For one-off access, create an ad hoc connection without editing the permanent hierarchy. It appears in the Connect To virtual group and is not persisted when RDCMan exits unless moved into a user-created group. Smart groups can also collect servers dynamically from rule-based criteria, while Favorites provides a flat working set across unrelated branches.

## Credentials, Gateways, and Security

RDCMan separates reusable authentication data from individual server definitions. Logon settings can contain a user name, password, and domain, with `domain\user` accepted as a combined identity. For machines using local rather than domain accounts, the domain field can use server or display-name substitution so one inherited configuration can log on to many hosts using each machine's local security context. Connect As temporarily overrides normal credentials for a single connection.

Credential profiles provide another level of reuse. A profile can be stored globally or inside an RDG file and referenced by groups that do not share a common parent. This suits an operations account used across several environments or separate credentials used by an RD Gateway and destination server. Updating the profile changes the stored password in one place.

Stored passwords can be protected with the local user's Windows data-protection context or with an X.509 certificate that has an accessible private key. File-scoped credential profiles follow the encryption configuration of their containing file, while global profiles follow the default group configuration. Certificate-based protection is useful when an RDG configuration must move between authorized workstations, provided the certificate and private key are transferred securely.

Gateway settings define the gateway host, authentication method, credential behavior, and local-address bypass. Specify gateways by fully qualified domain name. Treat resource redirection as a security decision: enable clipboard, drives, printers, ports, or smart cards only when the administrative workflow requires them.

## Managing Remote Sessions and Display

RDCMan's client area changes according to the selected tree node. Selecting a server opens its RDP client, while selecting a group presents thumbnails for servers in that group. Thumbnail behavior can be tuned per group: live preview may be enabled, disconnected systems may be hidden, and interaction with a live thumbnail can be allowed. Interactive thumbnails are convenient, but keyboard input can reach a remote session whose focus is not visually obvious. Disable thumbnail interaction when the view is used mainly for monitoring.

Individual servers can receive a larger thumbnail scale so a critical system remains usable while other sessions stay compact. Full-screen mode can be entered or left with `Ctrl+Alt+Break` by default. Multiple-monitor operation is available when configured, with the remote desktop arranged across a rectangular monitor area.

Remote desktop resolution is determined when the connection is established. RDCMan can use an explicit logical desktop size, match the client area, or use full screen. Changing this property while a server is connected does not resize the existing desktop; disconnect and reconnect, or use reconnect behavior that applies the new resolution.

For maintenance windows, the Reconnect virtual group is useful. If a server is unavailable during a reboot, placing it there causes repeated connection attempts until the host becomes reachable. Connected, Favorites, and Recent virtual groups provide alternative operational views without changing the RDG hierarchy. At session end, distinguish disconnect from logoff: disconnect preserves the Windows session, while logoff terminates it.

## Operational Control and Automation

RDCMan includes several features for operating large environments consistently. Find Servers searches the full `group\server` path with a regular-expression pattern, allowing an administrator to locate related hosts across different tree branches and act on the results. This is useful when host names encode site, role, or environment.

Startup behavior can be controlled globally or from the command line. RDCMan can reopen files loaded during the previous shutdown and remember which servers were connected. `/noopen` starts with an empty workspace, `/reconnect` reconnects previously active servers without prompting, `/noconnect` suppresses reconnection, `/c` initiates connections to specified servers, and `/reset` clears persisted preferences such as window position and size. These options are practical on jump hosts launched through administrative scripts or task-specific shortcuts.

Auto-save can periodically write open configuration files. An interval of zero disables periodic saves while suppressing the normal save prompt on exit, so use it carefully. When an older RDG file is opened and saved in the newer format, RDCMan creates a `filename.old` backup. Keep this copy until the converted configuration has been validated because the newer RDG format is not backward-compatible with older builds.

RDCMan can list remote sessions that were not opened from the application. The executing account needs Query Information rights on the target; disconnect and logoff require their corresponding permissions. This function also requires direct reachability rather than a gateway path. In managed environments, a machine policy registry value can disable RDCMan's logoff command, reducing the risk of terminating shared or production sessions accidentally.
