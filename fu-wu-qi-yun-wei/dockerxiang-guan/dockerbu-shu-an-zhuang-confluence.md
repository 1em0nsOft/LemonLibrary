```
docker run -v /data/data/confluence:/var/atlassian/application-data/confluence --name="confluence" -d -p 8090:8090 -p 8091:8091 --restart always 3e2c26db9138
```