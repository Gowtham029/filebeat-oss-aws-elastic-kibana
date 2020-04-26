# Configure Filebeat with AWS Elasticsearch and Kibana in centOS 7

### Prerequisites
    - Centos 7
    - AWS Elasticsearch 7.1.1
    - AWS Kibana 7.1.1
    - Filebeat OSS 7.6.2

### Download and install 

1.Download the filebeat OSS version:
```$ sudo rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch ```
2.Create a file with a .repo extension (for example, elastic.repo) in your/etc/yum.repos.d/ directory and add the following lines:
```
[elastic-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/oss-7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md 
```
3.Your repository is ready to use. For example, you can install Filebeat by running:
```$ sudo yum install filebeat ```
4.Configure filebeat to ship the data directly Elasticsearch
```$ vi /etc/filebeat/filebeat.yml```
5.Sample configuration filebeat.yml
``` 
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - '/path/to/location/*.log'

processors:
  - dissect:
      tokenizer: '{"level":"%{level}","severity":"%{severity}"}'
      field: "message"

output.elasticsearch:
  hosts: ["<aws:elasticsearch-url>:443"]
  index: "indexname-%{+yyyy.MM.dd}"

setup.kibana:
  host: "<aws-kibana-url>/_plugin/kibana/"

setup.ilm.enabled: false
setup.template.name: "<indexname>"
setup.template.pattern: "<indexname-*>"
```
Note:- Change the path based on the log file location ``` example: /var/www/logs/*log ```

#### Samples
filepath: ``` /var/www/logs/*.log```
Pattern: ``` {"level":"%{level}","severity":"%{severity}"} ```
Log samples: ```{"level":"Error","severity":"Critical"}```
Results:
```
{
  "level": "Error",
  "severity": "Critical"
}
```

##### References
1. https://www.elastic.co/guide/en/beats/filebeat/current/setup-repositories.html
2. https://www.elastic.co/guide/en/beats/filebeat/current/dissect.html
3. https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-configuration.html
