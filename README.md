Just copy and paste this command.

```
# Create and run the setup script
cat << 'EOF' > setup_public_ip.sh
#!/bin/sh

# Create the script to fetch public IP
echo '#!/bin/sh
curl -s ifconfig.me > /tmp/public_ip.txt' > /usr/bin/get_public_ip.sh

# Make the script executable
chmod +x /usr/bin/get_public_ip.sh

# Modify the LuCI status page to display the public IP
sed -i '/<h2 name="content"><%:Status%><\/h2>/a \
<h2><%:Public IP%></h2>\
<pre><%=luci.sys.exec("cat /tmp/public_ip.txt")%></pre>' /usr/lib/lua/luci/view/admin_status/index.htm

# Set up the cron job to update the public IP every minute and every 30 seconds
(crontab -l 2>/dev/null; echo "*/1 * * * * /usr/bin/get_public_ip.sh" ; echo "* * * * * sleep 30; /usr/bin/get_public_ip.sh") | crontab -

# Restart services
/etc/init.d/uhttpd restart
/etc/init.d/cron restart

echo 'Setup complete. Your public IP will be displayed on the LuCI status page and updated every 30 seconds.'
EOF

# Make the setup script executable and run it
chmod +x setup_public_ip.sh
./setup_public_ip.sh

```
![image](https://github.com/ForDefault/openwrt_get_public_ip/assets/106802923/cc304773-44ab-4205-8ab1-d4e6501d8cb1)
