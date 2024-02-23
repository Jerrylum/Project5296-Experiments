# Experiment 1

The time of downloading 1 file in 1GB, measured in seconds, in 10 different scenarios between 1 proxy 1 connection in total to 10 proxies 10 connections in total

Setting:
- Region: us-west-2
- Client: t3.medium
- Instances: t3.micro
- Server: t3.medium
- Server configuration: `limit_conn addr 10;` and `limit_rate 1000000;` which is 1MB/s

Server Side:
1. Connect to the instance via SSH
2. Execute the following commands:
```bash
cd Project5296-ServerNode
go run generate.go -m 1000
```
3. Modify `default.conf`
4. Run `sudo docker compose down && sudo docker compose up -d`

Local Side:
1. Execute the following commands:
```bash
cd Project5296-NetworkSetup
npm run create -- --instance-type t3.micro --ip-count 10
```

Client Side:
1. Connect to the instance via SSH
2. Copy the instances.txt from the local side to Project5296-ClientTool/etc/instance.txt
3. Execute the following commands:
```bash
cd Project5296-ClientTool
for i in {1..10}; do head -n $i etc/instance.txt > "etc/instance$i.txt"; done
echo "http://(Server's IP)/download/1000.out > /dev/null" > etc/LINKS.txt
for i in {1..10}; do go run . -connections $i -proxies etc/instance$i.txt -requests etc/LINKS.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment1_Result.log; done
```
4. Download timelog to local
5. Exit the SSH session

After all ended, back to Local vscode window and run:
npm run destroy
