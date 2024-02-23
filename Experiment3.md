# Experiment 3

The time of downloading 900MB of file(s), measured in seconds, in 10 different scenarios via 5 proxy 5 connections in total, including:
- 1 x 900MB file (each proxy downloads 1 file segemnt in 180MB)
- 5 x 180MB files (each proxy downloads 1 file)
- 10 x 90MB files (each proxy downloads 2 files)
- 15 x 60MB files (each proxy downloads 3 files)
- 20 x 45MB files (each proxy downloads 4 files)
- 25 x 36MB files (each proxy downloads 5 files)
- 30 x 30MB files (each proxy downloads 6 files)
- 35 x 28.5714MB files (each proxy downloads 7 files)
- 40 x 25MB files (each proxy downloads 8 files)
- 45 x 22.5MB files (each proxy downloads 9 files)

Setting:
- Region: us-west-2
- Client: t3.medium
- Instances: t3.micro
- Server: t3.medium
- Server configuration: `limit_conn addr 5;` and `limit_rate 1000000;` which is 1MB/s

Server Side:
1. Connect to the instance via SSH
2. Execute the following commands:
```bash
cd Project5296-ServerNode
go run generate.go -m 900
go run generate.go -m 180
go run generate.go -m 90
go run generate.go -m 60
go run generate.go -m 45
go run generate.go -m 36
go run generate.go -m 30
go run generate.go -m 28.5714
go run generate.go -m 25
go run generate.go -m 22.5
```
3. Modify `default.conf`
4. Run `sudo docker compose down && sudo docker compose up -d`

Local Side:
1. Execute the following commands:
```bash
cd Project5296-NetworkSetup
npm run create -- --instance-type t3.micro --ip-count 5
```

Client Side:
1. Connect to the instance via SSH
2. Copy the instances.txt from the local side to Project5296-ClientTool/etc/instance.txt
3. Execute the following commands:
```bash
cd Project5296-ClientTool
echo "http://(Server's IP)/download/900.out" > etc/LINKS1.txt
for i in {1..5}; do echo "http://(Server's IP)/download/180.out > /dev/null" >> etc/LINKS2.txt; done
for i in {1..10}; do echo "http://(Server's IP)/download/90.out > /dev/null" >> etc/LINKS3.txt; done
for i in {1..15}; do echo "http://(Server's IP)/download/60.out > /dev/null" >> etc/LINKS4.txt; done
for i in {1..20}; do echo "http://(Server's IP)/download/45.out > /dev/null" >> etc/LINKS5.txt; done
for i in {1..25}; do echo "http://(Server's IP)/download/36.out > /dev/null" >> etc/LINKS6.txt; done
for i in {1..30}; do echo "http://(Server's IP)/download/30.out > /dev/null" >> etc/LINKS7.txt; done
for i in {1..35}; do echo "http://(Server's IP)/download/28.5714.out > /dev/null" >> etc/LINKS8.txt; done
for i in {1..40}; do echo "http://(Server's IP)/download/25.out > /dev/null" >> etc/LINKS9.txt; done
for i in {1..45}; do echo "http://(Server's IP)/download/22.5.out > /dev/null" >> etc/LINKS10.txt; done
for i in {1..10}; do go run . -connections 5 -proxies etc/instances.txt -requests etc/LINKS$i.txt -log logs/"$(date -Ins).log" -name Attempt$i -timeLog Experiment3_Result.log; done
```
4. Download timelog to local
5. Exit the SSH session

After all ended, back to Local vscode window and run:
npm run destroy
