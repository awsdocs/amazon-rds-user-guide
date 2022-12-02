# Hooks reference for Trusted Language Extensions for PostgreSQL<a name="PostgreSQL_trusted_language_extension-hooks-reference"></a>

Trusted Language Extensions for PostgreSQL supports PostgreSQL hooks\. A *hook* is an internal callback mechanism available to developers for extending PostgreSQL's core functionality\. By using hooks, developers can implement their own functions or procedures for use during various database operations, thereby modifying PostgreSQL's behavior in some way\. For example, you can use a `passcheck` hook to customize how PostgreSQL handles the passwords supplied when creating or changing passwords for users \(roles\)\.

View the following documentation to learn about the hooks available for your TLE extensions\.

**Topics**
+ [Password\-check hook \(passcheck\)](passcheck_hook.md)