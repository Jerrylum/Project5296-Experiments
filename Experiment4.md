# Experiment 4

The time of download n file in 5GB, measured in seconds, where n is the number of connections, in 4 x 7 different scenarios with 1 proxy as follows:
|             |t2.nano|t2.micro|t3.nano|t3.micro|
|-------------|-------|--------|-------|--------|
|1 Connection |       |        |       |        |
|2 Connections|       |        |       |        | 
|3 Connections|       |        |       |        |
|4 Connections|       |        |       |        |
|5 Connections|       |        |       |        |
|6 Connections|       |        |       |        |
|7 Connections|       |        |       |        |

Setting:
- Region: us-west-2
- Client: t3.medium
- Server: t3.medium
- Server configuration: `limit_conn addr 10;` and `limit_rate 50000000;` which is 50MB/s

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
for i in {1..2}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS2.txt; done
for i in {1..3}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS3.txt; done
for i in {1..4}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS4.txt; done
for i in {1..5}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS5.txt; done
for i in {1..6}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS6.txt; done
for i in {1..7}; do echo "http://(Server's IP)/download/5000.out > /dev/null" >> etc/LINKS7.txt; done
#Go through all 5 column with 1-5 connection 
for i in {1..7}; do go run . -connections $i -proxies etc/instances1.txt -requests etc/LINKS$i.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment4_t2.nano_Result.log; done
for i in {1..7}; do go run . -connections $i -proxies etc/instances2.txt -requests etc/LINKS$i.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment4_t2.micro_Result.log; done
for i in {1..7}; do go run . -connections $i -proxies etc/instances3.txt -requests etc/LINKS$i.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment4_t3.nano_Result.log; done
for i in {1..7}; do go run . -connections $i -proxies etc/instances4.txt -requests etc/LINKS$i.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment4_t3.micro_Result.log; done
```
4. Download timelog to local
5. Exit the SSH session

After all ended, back to Local vscode window and run:
npm run destroy
