🔎 Elastic Queries Used During Investigation
Suspicious POST Requests
_index: weblogs AND http.request.method:POST AND client.ip:"203.0.113.55"

Web Shell Command Execution
_index: weblogs AND http.request.method:GET AND client.ip:"203.0.113.55" AND errorEE.aspx

Administrator Login
winlog.event_id:4624 AND host.name:"winserv2019.some.corp"

New User Creation
winlog.event_id:4720

Privilege Escalation Commands
process.parent.name:cmd.exe AND user.name:"Administrator"
