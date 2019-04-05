# BuildingCustoDockerImage
Building a Custom Docker Image
Building a Custom Docker Image
1.To log on, click Administrator, type the password Pa$$w0rdLinux, and then click Sign In.
2.At the bottom-left of the desktop, click Show Applications, and then click Terminal.
3.To ensure you are in the home folder, type the following command, and then press Enter:
cd ~
4.To create and edit an empty text file, type the following command, and then press Enter:
nano Dockerfile
5.In the nano text editor, enter the following lines of text:

FROM microsoft/mssql-server-linux:latest
COPY containers/mssql-custom-image-example/SampleDB.bak /var/opt/mssql/data/SampleDB.bak
CMD ["/opt/mssql/bin/sqlservr"]

6.Press Esc, and then press Ctrl+X.
7.When asked if you want to save changes, press y, and then press Enter.
8.To check the contents of the Dockerfile, type the following command, and then press Enter:
cat Dockerfile

Build an image from the Docker file
1.To build the new image from the Dockerfile, type the following command, and then press Enter:
sudo docker build . -t mssql-with-test-database
2.At the Password prompt, type Pa$$w0rdLinux, and then press Enter.
3.When the build is complete, to list the images available, type the following command, and then press Enter:
sudo docker image ls
4.To start the container, type the following command, and then press Enter:
sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Pa$$w0rdSQL' \
  -p 1500:1433 --name sqltestcontainer2 \
  -d mssql-with-test-database

Restore the database
1.In the terminal, to restore the database file list, type the following command, and then press Enter:
sudo docker exec -it sqltestcontainer2 /opt/mssql-tools/bin/sqlcmd \
-S localhost -U SA -P 'Pa$$w0rdSQL' \
-Q 'RESTORE FILELISTONLY FROM DISK = "/var/opt/mssql/data/SampleDB.bak"' \
-W \
| tr -s ' ' | cut -d ' ' -f 1-2

2.To restore the database, type the following command, and then press Enter:
sudo docker exec -it sqltestcontainer2 /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U sa -P 'Pa$$w0rdSQL' \
   -Q 'RESTORE DATABASE ProductCatalog FROM DISK = "/var/opt/mssql/data/SampleDB.bak" WITH MOVE "ProductCatalog" TO "/var/opt/mssql/data/ProductCatalog.mdf", MOVE "ProductCatalog_log" TO "/var/opt/mssql/data/ProductCatalog.ldf"'

Run a query
1.To start Azure Data Studio, type azuredatastudio, and then press Enter.
2.If the Connection window appears, click Cancel.
3.On the File menu, click New Query.
4.In the query window, type the following code:
SELECT ProductID, Name, Price
FROM ProductCatalog.dbo.Product

5.To execute the query, in the top-left of the query window, click Run.
6.In the Connection window, in the Server box, type localhost, 1500.
7.In the User name box, type sa, in the Password box, type Pa$$w0rdSQL, and then click Connect.


8.Examine the results of the query, and then close Azure Data Studio.


Stop and delete the container

1.Switch to the terminal.
2.To stop the container you have just deployed, type the following command, and then press Enter:
sudo docker stop sqltestcontainer2
3.To delete the container, type the following command, and then press Enter:
sudo docker container rm sqltestcontainer2

Unit 7: Using Docker Composer to deploy a multi-container application

Install Docker Compose

1.To log on, click Administrator, type the password Pa$$w0rdLinux, and then click Sign In.
2.At the bottom-left of the desktop, click Show Applications, and then click Terminal.
3.To download Docker Compose, type the following command, and then press Enter:
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

4.At the Password prompt, type Pa$$w0rdLinux, and then press Enter.
5.To mark the downloaded file as executable, type the following command, and then press Enter:
sudo chmod +x /usr/local/bin/docker-compose

6.To create a link to the downloaded executable, type the following command, and then press Enter:
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

Complete a docker-compose.yml file

1.In the terminal, to change to the right folder, type the following command, and then press Enter:
cd ~/containers/mssql-aspcore-example
2.To edit the docker-compose.yml file, type the following command, and then press Enter:
nano docker-compose.yml
3.In the editor, replace the password safePASSWORD123 with the password Passw0rdSQL.

 
Do not use dollar signs in this password as in previous units. This is because dollar signs are interpreted differently in a YAML file.

4.To close the file, press Esc, and then press Ctrl+X.
5.To save the changes, press y, and then press Enter.
6.To edit the password in the db-init.sh file, type the following command, and then press Enter:
nano ./mssql-aspcore-example-db/db-init.sh

7.In the editor, replace the password safePASSWORD123 with the password Passw0rdSQL.
8.To close the file, press Esc, and then press Ctrl+X.
9.To save the changes, press y, and then press Enter.

Start the containers

1.To start the docker, type the following command, and then press Enter:
sudo systemctl start docker
2.To enable Docker Compose to download the necessary images and start the containers, type the following command, and then press Enter:
sudo docker-compose up --build

Connect to the database by using Azure Data Studio
1.To start a new instance of the terminal, at the bottom-left of the desktop, click Show Applications, click All, and then click Terminal.
2.To start Azure Data Studio, type azuredatastudio, and then press Enter.
3.In Azure Data Studio, if the Connection window is not opened, on the View menu, click Servers, and then click Add Connection.
4.In the Connection window, in the Server box, type localhost, 1500.
5.In the User name box, type sa.
6.In the Password box, type Passw0rdSQL, and then click Connect.
7.To check the tables in the new database, in the list of servers, under localhost, 1500, <default> (sa), expand Databases, expand ProductCatalog, and then expand Tables.

Browse the website from the containers
1.To start Firefox, in the top left of the desktop, click Firefox Web Browser.
2.In the Address bar, type localhost:5000, and then press Enter.
3.On the homepage, click Product Catalog Demo, and note the products are displayed. You can investigate individual products.
4.Close Firefox, close Azure Data Studio, and then close the terminal which was used to open Azure Data Studio.

Shut down and remove the docker images

1.To stop Docker Compose, in the remaining terminal, press Ctrl+C.
2.To remove the containers and images, type the following command, and then press Enter:
sudo docker-compose down --rmi all


