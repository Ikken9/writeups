
Get-Content C:\fso\a.txt | Measure-Object –Word


netstat
netstat -l



https://stackoverflow.com/questions/27951561/use-invoke-webrequest-with-a-username-and-password-for-basic-authentication-on-t

Invoke-WebRequest -Uri 127.0.0.1:1225/endpoints/13 -Headers $Headers -OutFile file13.txt | Get-Content file13.txt | Measure-Object –Word


