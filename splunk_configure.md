# Splunk and Splunk forwarder configuration
- Splunk AMI
    - AMI to Deploy: *`splunk_AMI_8.0.2_2020-02-13_12-50-36-7b65de6c-5006-4ca2-bd75-fdba95ae5d9d-ami-00c4b7f4844043f63.4 (ami-074c26b353fa26e91)`*

- Splunk SSL self-signed certificate configuration if assigning an elastic or new IP after initial launch
    - https://wiki.splunk.com/Community:SplunkWeb_SSL_SelfSignedCert_NewRootCA

- Splunk Forwarder (RHEL Host)
    - Splunk Forwarder RPM: *`wget -O splunkforwarder-8.0.2-a7f645ddaf91-linux-2.6-x86_64.rpm 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=8.0.2&product=universalforwarder&filename=splunkforwarder-8.0.2-a7f645ddaf91-linux-2.6-x86_64.rpm&wget=true'`*
    
    - Install RPM
        - *`rpm -ivh splunkforwarder-*.rpm`*

    - Add Forward-Server
        - *`cd /opt/splunkforwarder/bin`*
        - *`./splunk add forward-server {{server_ip}}:9997`*
    
    - List forward-server
        - *`./splunk list forward-server`*
        > *Restart Splunk if not active*  
        > *`./splunk restart`*

<!-- 
sudo su -
cd /opt/splunkforwarder/bin
./splunk start

-->

- Splunk UI
    - *`{{ server_ip }}:8000`*
        > login {{instance-id}} == AWS instance id:  
        > *`admin / SPLUNK-{{instance_id}}`*  
            > 
    - Add an alert action:
        - *Settings > Searches, reports and alerts > New Alert*
        - *New Alert*
        - *Add Title*
        - *Add Search*
            - *`index=main source=/var/log/audit/audit.log visudo`*
        - *Alert Type*
            - *`Real-time`*
        - *Trigger alert when*
            - *`is greater than: 3`*
            - *`in 1 minute(s)`*
        - *Throttle*
            - *`240 second(s)`*
        - *Add Actions*
            - *`Add to Triggered Alerts - Medium`*
            - *`Run a script - {{ script_name }}`*

- Splunk Forwarder (RHEL Host)
    - Edit audit rules
    - *`vim /etc/audit/rules.d/audit.rules`*
        - *Add the following to EOF*
        - *`-w /etc/sudoers -p wa -k sudoers`*
    - Load changes
    - *`augenrules --load`*
    - *`Add file to monitor`*
        - *`./opt/splunkforwarder/splunk add monitor /var/log/audit/audit.log`*