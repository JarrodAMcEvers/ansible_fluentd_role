Example of FluentD configuration that sends syslog and /var/log/auth.log contents to an S3 bucket:
```yaml
fluentd: |
  <source>
    @type syslog
    port 5140
    tag syslog
  </source>
  <source>
    @type tail
    path /var/log/auth.log
    pos_file /var/log/td-agent/tmp/auth.log.pos
    tag auth
    <parse>
      @type none
    </parse>
  </source>

  <match *>
    @type s3

    s3_bucket my-awesome-bucket
    s3_region us-east-1

    path logs
    s3_object_key_format "%{path}/#{Socket.gethostname}/syslog_authlog_%{time_slice}_%{index}.%{file_extension}"
    time_slice_format %Y%m%d-%H
    timekey 1h
    time_slice_wait 10m
    timezone America/Chicago
    format json
  </match>
```
