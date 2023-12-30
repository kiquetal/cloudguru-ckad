- Create a docker file to place data1.txt into /etc/data/mainData.txt
 Add data2.txt to /etc/data/data2.txt, and data3.txt to /etc/data/data3.txt

```dockerfile
FROM busybox:1.34.1
COPY data1.txt /etc/data/mainData.txt
ADD data2.txt /etc/data/data2.txt
ADD data3.txt /etc/data/data3.txt
```

- Save container image as tar file
```bash
docker build -t data-image .
```
```bash
docker save -o /home/cloud_user/data-image.tar buzz:latest
```
