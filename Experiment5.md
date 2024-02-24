# Experiment 5

The time of download a file in 5GB, measured in seconds, with no request processing rate per IP limitation, in 4 x 4 different scenario with 1 proxy as follows:
|             |t2.nano|t2.micro|t3.nano|t3.micro|
|-------------|-------|--------|-------|--------|
|1 Connection |       |        |       |        |
|2 Connections|       |        |       |        | 
|3 Connections|       |        |       |        |
|4 Connections|       |        |       |        |

Setting:
- Region: us-west-2
- Client: t3.medium
- Server: t3.medium
- Server configuration: `limit_conn addr 10;` and `limit_rate 0;` which is no limitation

Server Side:
1. Connect to the instance via SSH
2. Execute the following commands:
```bash
cd Project5296-ServerNode
go run generate.go -m 5000
```
3. Modify `default.conf`
4. Run `sudo docker compose down && sudo docker compose up -d`

Local Side:
1. Run follow:
```bash
cd Project5296-NetworkSetup
npm run create -- --instance-type t2.nano --ip-count 1
mv instances.txt instances1.txt
npm run create -- --instance-type t2.micro --ip-count 1
mv instances.txt instances2.txt
npm run create -- --instance-type t3.nano --ip-count 1
mv instances.txt instances3.txt
npm run create -- --instance-type t3.micro --ip-count 1
mv instances.txt instances4.txt
```

Client Side:
1. Connect to the instance via SSH
2. Copy all the instances?.txt from the local side to Project5296-ClientTool/etc/
3. Execute the following commands:
```bash
cd Project5296-ClientTool
echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS1.txt
for i in {1..4}; do go run . -connections $i -proxies etc/instances1.txt -requests etc/LINKS1.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment5_t2.nano_Result.log; done
for i in {1..4}; do go run . -connections $i -proxies etc/instances2.txt -requests etc/LINKS1.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment5_t2.micro_Result.log; done
for i in {1..4}; do go run . -connections $i -proxies etc/instances3.txt -requests etc/LINKS1.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment5_t3.nano_Result.log; done
for i in {1..4}; do go run . -connections $i -proxies etc/instances4.txt -requests etc/LINKS1.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment5_t3.micro_Result.log; done
```
4. Download timelog to local
5. Exit the SSH session

After all ended, back to Local vscode window and run:
npm run destroy
