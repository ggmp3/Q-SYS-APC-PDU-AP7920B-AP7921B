# Q-SYS-APC-PDU-AP7920B-AP7921B
Q-Sys User Component

# APC AP7920/7921B Networked PDU (SNMP Control)
-- Written by Glen Gorton
-- Built with Firmware v6.8.0. Tested and working on v6.9.6

## NOTES:
-- SNMP Setup:
1. Browse to the HTTP interface of the PDU, select Configuration->Network->SNMPv3->Access and Enable Access for SNMPv3.
2. Browse to the HTTP interface of the PDU, select Configuration->Network->SNMPv3->User Profiles. Change User Name (eg. "apc snmp profile1") if desired. (Authentication Passphrase and Privavy Passphrase are OFF by default).
3. Browse to the HTTP interface of the PDU, select Configuration->Network->SNMPv3->Access Control. Check Enable for the user profile you're using.

## 19th October 2023
-- Some changes made to the snmp_session.ErrorHandler as QSC have advised there are six possible errors that can be returned: "Timed out", "Send Failed", "Sec Error", "SNMP Error", "No Such Instance" and "No Such Object"
-- Created 'compromised' Event Logging for the errors: "Send Failed", "Sec Error", "No Such Instance" and "No Such Object"

## 15th September 2023
-- QSC have fixed (with Designer v9.9.0) the snmp_session.ErrorHandler which will now provide a response if there is a NULL response. (ie. unsupported OID sent to device).
-- Designer v9.4.4 to v9.8.2 will crash (when emulating) or a running Core Design will eventually restart because it does not support a NULL response. Versions prior to v9.4.4 worked!
-- Found that script appeared to become unstable on QSC Firmware v9.4.4 and up, possibly also because of too many SNMP sessions running concurrently. snmp_session:startSession() now only runs once at Connect(), not for every OID sent.
-- StartSNMPSession() function added to start single the snmp_session at Connect().
-- Added ActiveQueryList OID table. Entries are not longer removed then added back into the QueryList table. The items number being polled now increments so the table is never modified.
-- sendDelaySet() function and associated Timers all removed.
-- Added Controls["Restart Script"]:Trigger() when IP Address is changed.
-- Removed Helper Function debugFormat() as it was not used.
-- Added Connect() function to SNMPnoresponse() function so that the device continues to try and re-establish a connection.
-- snmp_session.ErrorHandler will print errors and  SetStatus() depending on the error received (eg. "Timed out" will SetStatus to Compromised after a number of TimeOuts)
-- 'Cold Start Delay' is now a combo box with selectable controls. Was previously just a text indicator.
-- Added 'Cold Start Delay Time' (sec) integer input that works in conjunction with 'Cold Start Delay' control.

## 2nd December 2022
-- Added "Log Entry" and "Log Severity" to TextIndicators to the reset when script Initializes.
-- EventLog() function has been updated as the faster ipairs interating through the EventLogs table was not reliable when wired to a Monitoring Proxy.

## 1st Decemeber 2021
-- Added 'Outlet And Power Event Logging' toggle button per outlet so event logs can be turned on per outlet.

## 17th Novemeber 2021
-- Added 'Outlet And Power Event Logging' toggle button that must be True to enable below added functions:
-- Added 'OutletDrawingPowerStatus()' function which keeps track of outlet power state and power draw, logs events for off/on transitions and unexpected power loss.
-- Event Logs are now pushed to the EventLogs table in the output 'trigger' to Monitoring Proxy cannot handle multiple triggers at once. EventLogs table is interated through using EventLog().
-- Any future event logs need to be added to the table. Eg. table.insert(EventLogs, {log = "Outlet "..i.." with connected device ("..ControlName..") has been turned OFF." , severity = "warning"})
