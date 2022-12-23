# 2 - Working with remote servers

## Theory [2]

- [0.4] What are computer ports on a high level? How many ports are there on a typical computer?

A port is a virtual point where network connections start and end. Ports are software-based and managed by a computer's operating system. Each port is associated with a specific process or service. Ports allow computers to easily differentiate between different kinds of traffic: emails go to a different port than webpages, for instance, even though both reach a computer over the same Internet connection.

There are 65,535 ports on a typical computer, numbered from 0 to 65,535. These ports are used by different programs and protocols to communicate with each other and with other devices on a network. However, not all of these ports are available for use by programs and protocols. Some of them are reserved for specific purposes and are not meant to be used by user programs.

- [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.

HTTP (Hypertext Transfer Protocol) is a standard protocol for transmitting data on the World Wide Web. It is used to transfer data from a web server to a web browser in order to display web pages and other content.

HTTPS (HTTP Secure) is an extension of HTTP that adds an extra layer of security by encrypting the data being transmitted between a web server and a web browser. It uses the same underlying structure as HTTP, but adds an encryption layer using the Secure Sockets Layer (SSL) or Transport Layer Security (TLS) protocols.

SSH (Secure Shell) is a network protocol used to securely connect to a remote computer. It allows you to remotely log in to a computer and execute commands, as well as transfer files between computers. It is typically used to remotely manage servers and other network devices.

These protocols are similar in that they are all used to transmit data over a network. However, they differ in the specific purpose and level of security they provide.

Here are the default ports for some common data transfer protocols:

HTTP: port 80
HTTPS: port 443
SSH: port 22
FTP (File Transfer Protocol): port 21
SMTP (Simple Mail Transfer Protocol): port 25
Telnet: port 23
POP3 (Post Office Protocol version 3): port 110
IMAP (Internet Message Access Protocol): port 143

- [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser.

1. IP (Internet Protocol) is a set of rules that govern how data is transmitted over the internet. It defines how data is packaged into packets and sent from one device to another, as well as how devices on the network should address and route those packets.

2. IPs that are called 'white' or 'public' are IP addresses that are visible on the internet and can be accessed by anyone. These IPs are assigned to servers, websites, and other devices that need to be accessible from the internet.

3. When you enter 'google.com' into a web browser, the following process occurs:
   1. The web browser looks up the IP address associated with 'google.com' using the Domain Name System (DNS).
   2. The web browser sends an HTTP request to the IP address of the Google web server.
   3. The Google web server responds with the requested webpage and any related resources, such as images and stylesheets.
   4. The web browser receives the response and renders the webpage for the user to view.

- [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.

NGINX is a web server and reverse proxy that is known for its high performance, stability, and low resource usage. It is often used to handle high traffic websites and can also be used as a load balancer to distribute traffic across multiple servers.

On a high level, Nginx works by listening for incoming HTTP requests and forwarding them to the appropriate server or application. It can also cache static content, such as images and stylesheets, to improve the performance of websites.

Some alternative web servers include:

Apache: A popular open-source web server that is widely used on the internet.
Microsoft IIS: A web server that is included with the Windows operating system.
Lighttpd: A lightweight web server that is optimized for high performance.
Cherokee: A web server that is designed to be fast and easy to configure.
HAProxy: A load balancer and reverse proxy that is commonly used in high-traffic environments.

- [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail.

SSH (Secure Shell) is a network protocol that is used to securely connect to a remote computer. It allows you to remotely log in to a computer and execute commands, as well as transfer files between computers. It is typically used to remotely manage servers and other network devices.

There are two main ways to authenticate in an SSH server:

1. Password-based authentication: In this method, the user must provide a valid username and password to log in to the SSH server. The password is sent over the network in an encrypted form, so it is secure even if the connection is being monitored.
2. Public key authentication: In this method, the user generates a pair of public and private keys using a program such as ssh-keygen. The public key is placed on the SSH server, and the private key is kept on the user's local computer. When the user tries to log in to the SSH server, the server uses the public key to encrypt a challenge message and sends it back to the user. The user's local computer uses the private key to decrypt the message and sends the decrypted message back to the server as proof of identity. This method is more secure than password-based authentication because it does not rely on sending a password over the network.

## Problem [6.5]

A real-life situation that occurred to me several times over the years.

Imagine wrapping up a large bioinformatics project and wanting to share raw data with your colleagues in a friendly and straightforward format. The best option would be to use an online genome browser and host your data remotely, so it is easily accessible by anyone with a valid link. This is exactly what we will be doing here.

**Remote Server**:

- [2] Create a new virtual machine in the Yandex/Mail/etc cloud (order at least 10GB of free disk space). Generate SSH key pair and use it to connect to your server.

Public IPv4: 51.250.72.119

- [1] Download the latest human genome assembly (GRCh38) from the Ensemble FTP server ([fasta](https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz), [GFF3](https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz)). Index the fasta using samtools (`samtools faidx`) and GFF3 using tabix.

Downloading the latest human genome assembly and annotation:

```
wget https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
wget https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz
```

Indexing the genome:

```
sudo apt-get install samtools
gzip -d Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
samtools faidx Homo_sapiens.GRCh38.dna.primary_assembly.fa
```

Indexing the annotation:

```
sudo apt-get install tabix
gzip -d Homo_sapiens.GRCh38.108.gff3.gz
(grep "^#" Homo_sapiens.GRCh38.108.gff3; grep -v "^#" Homo_sapiens.GRCh38.108.gff3 | sort -t"`printf '\t'`" -k1,1 -k4,4n) | bgzip > Homo_sapiens.GRCh38.108.sorted.gff3.gz
tabix -p gff Homo_sapiens.GRCh38.108.sorted.gff3.gz
```

- [1] Select and download BED files for three ChIP-seq and one ATAC-seq experiment from the ENCODE (use one tissue/cell line). Sort, bgzip, and index them using tabix.

Cell line: *Homo sapiens* GM12878 — [ATAC](https://www.encodeproject.org/experiments/ENCSR637XSC/) 
TF ChIP-seq — [1](https://www.encodeproject.org/experiments/ENCSR072PWP/), [2](https://www.encodeproject.org/experiments/ENCSR412QBS/), [3](https://www.encodeproject.org/experiments/ENCSR987MTA/)

```
wget -O ATAC.bed.gz "https://www.encodeproject.org/files/ENCFF654BVB/@@download/ENCFF654BVB.bed.gz" wget -O ZNF24.bed.gz "https://www.encodeproject.org/files/ENCFF268HSQ/@@download/ENCFF268HSQ.bed.gz" 
wget -O TARDBP.bed.gz "https://www.encodeproject.org/files/ENCFF110LBD/@@download/ENCFF110LBD.bed.gz" 
wget -O BHLHE40.bed.gz "https://www.encodeproject.org/files/ENCFF599AUJ/@@download/ENCFF599AUJ.bed.gz"
```

Unpacking and sorting:

```
gzip -d *bed.gz
for i in *.bed; do sort -k 1,1 -k2,2n $i > $(echo $i| cut -d '.' -f 1)'_sorted.bed'; done
```

The names of the chromosomes in gff and bed are different, so they must be renamed in the bed file («chr1» to «1»):

```
for i in *.sorted.bed; do awk '{gsub(/^chr/,""); print}' $i > $(echo $i| cut -d '.' -f 1)'_renamed.bed'; done

#Packing and indexing
for i in *sorted_renamed.bed; do bgzip -c $i > $i'.gz'; done
for i in *sorted_renamed.bed.gz; do tabix -p bed $i; done
```

**JBrowse 2**

- [1] Download and install [JBrowse 2](https://jbrowse.org/jb2/). Create a new jbrowse [repository](https://jbrowse.org/jb2/docs/cli/#jbrowse-create-localpath) in `/mnt/JBrowse/` (or some other folder).

```
sudo wget https://github.com/GMOD/jbrowse-components/releases/download/v2.3.2/jbrowse-web-v2.3.2.zip
sudo apt-get install unzip
sudo unzip jbrowse-web-v2.3.2.zip
sudo rm jbrowse-web-v2.3.2.zip
```

- [0.25] Install nginx and amend its config ([/etc/nginx/nginx.conf](https://colab.research.google.com/drive/1NVigSM_GHff3dSrKLU5VFo8gXw-lhqh0#)) to contain the following section:

  ```
  http {
  # Don't touch other options!
  # ........
  # ........
  
  # Comment this line(!):
  # include /etc/nginx/sites-enabled/*;
  
  # Add this:
  server {
    listen 80 default_server;
    index index.html;
    server_name _;
  
    # Don't put JBrowse inside the home directory!
    # You will have problems with permissions
    location /jbrowse/ {
      alias /mnt/JBrowse/;    
    }
  }
  }
  ```

Installing nginx and amending its config:

```
sudo apt install nginx
sudo nano /etc/nginx/nginx.conf 
```

- [0.25] Restart the nginx (reload its config) and make sure that you can access the browser using a link like this: `http://64.129.58.13/jbrowse/`. Here `64.129.58.13` is your public IP address.

```
sudo nginx -s reload
```

It didn't work the first time, so I tried to follow the [guide](https://jbrowse.org/jb2/docs/quickstart_web/) 

```
#Left only this part in config
server { 
  listen 80; 
  index index.html;
} 

sudo jbrowse create /var/www/html/jbrowse/
```

- [1] Add your files (BED & FASTA & GFF3) to the genome browser and verify that everything works as intended. Don't forget to [index](https://jbrowse.org/jb2/docs/cli/#jbrowse-text-index) the genome annotation, so you could later search by gene names.

```
sudo jbrowse add-assembly Homo_sapiens.GRCh38.dna.primary_assembly.fa --load copy --out /var/www/html/jbrowse/
sudo jbrowse add-track Homo_sapiens.GRCh38.108.sorted.gff3.gz --out /var/www/html/jbrowse/
sudo jbrowse add-track ZNF24_sorted_renamed.bed.gz --load copy --out /var/www/html/jbrowse/
sudo jbrowse add-track TARDBP_sorted_renamed.bed.gz --load copy --out /var/www/html/jbrowse/
sudo jbrowse add-track BHLHE40_sorted_renamed.bed.gz --load copy --out /var/www/html/jbrowse/
```

http://51.250.72.119/jbrowse/



- [0.5] Give an in-depth explanation of the OSI model and how the TCP/IP stack works. Don't copy-paste descriptions from the internet; paraphrase and shorten as much as possible (imagine writing a cheat sheet for yourself).

The OSI model is a framework that defines how different networking protocols work together to enable communication between devices on a network. It is divided into seven layers: 

1. the physical layer (transmitting data over a physical medium), 
2. the data link layer (establishing and maintaining a connection on the same local network), 
3. the network layer (routing data between networks using logical addresses), 
4. the transport layer (establishing and maintaining end-to-end connections and providing reliability), 
5. the session layer (establishing, maintaining, and terminating connections), 
6. the presentation layer (translating data between different formats),
7. the application layer (enabling communication between applications on different devices).

The TCP/IP stack is a suite of protocols that is used to enable communication on the Internet and other networks. It is based on the OSI model, but has some differences in the way it organizes the layers. It is divided into four layers: 

1. the network interface layer (physical and data link layers), 
2. the internet layer (network layer), 
3. the transport layer (transport layer),
4. the application layer (session, presentation, and application layers).
