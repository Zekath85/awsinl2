# AWS Inlämningsuppgift 2
### Skapa en robust och säker och eventuellt skalbar wordpress-site som utnyttjar EFS (Elastic File System) och RDS (Relational Database Service).

Denna uppgift är löst med hjälp av Cloudformations och ett bash-script. Genom att använda CloudFormation kan hela infrastrukturen skapas automatiskt och konsekvent, vilket minskar risken för mänskliga fel och säkerställer att samma konfiguration används i varje distribution.

Kör scriptet via kommando:
```
aws cloudformation create-stack --stack-name Demo \
  --template-body file://wordpress_CF.yaml \
  --parameters ParameterKey=DBPassword,ParameterValue=dittlösenord\
  --capabilities CAPABILITY_NAMED_IAM
```

Infrastuktur:
![image](https://github.com/user-attachments/assets/053cbb37-bda8-412a-bc4b-eb4d7c242bb6)
__

# Förklarning av scriptet

En provision-server (EC2) hostar wordpress-site med RDS (MariaDB) ansluten till sig som vår databas. EFS har tre stycken mountpoints ansluta till sig som respektive koppas ihop med ett subnät. Våran wordpress-site mapp på Provision-servern är replikerad till mounttargets och genom att fästa dessa mot VM i ASG möjliggör vi att de får återkomst till sidan. Launch template förklarar inställningarna för dessa VM och gruppen skalas automatiskt upp till fler VM vid behov/ökad belastning utefter vår template. ALB (lastbalanseraren) hanterar all inkommande HTTP trafik och för ökad säkerhet har flertal resurser lagts i separata säkerhetsgrupper. 

## Resurser:
### DemoVPC:
Skapar ett VPC (Virtual Private Cloud) för isolerad nätverkssegmentering, vilket skyddar resurser från externa hot.

### Public Subnets: 
Tre offentliga subnät i olika zoner, vilket säkerställer hög tillgänglighet och skalbarhet.

### Internet Gateway & VPC Gateway Attachment: 
Möjliggör utgående och inkommande internettrafik till resurser i VPC.

### Public Route Table & Associations: 
Skapar en rutt till internet och associerar den med subnäten, vilket gör att instanser i subnäten kan kommunicera externt.

### EFS och Mount Targets: 
Skapar och ansluter ett delat EFS-filsystem för applikationsdata, tillgängligt i alla zoner för redundans.

### RDS och Subnet Group: 
En MariaDB RDS-instans skapas, endast tillgänglig inom VPC och skyddad av säkerhetsgrupper, vilket skyddar databasåtkomst.

## Säkerhetsfördelar
Isolering i VPC och Subnät: Genom att använda ett VPC och separata subnät minskas risken för obehörig åtkomst till interna resurser.
Kontrollerad Nätverksåtkomst via Säkerhetsgrupper: Varje resurs skyddas av en specifik säkerhetsgrupp, vilket endast tillåter nödvändig trafik och blockerar övrig oönskad åtkomst.
Lastbalanseraren skyddar backend-servrar från att exponeras direkt mot internet och säkerställer att bara godkända portar är öppna.
Genom att ange variabler som lösenord och användarnamn med NoEcho skyddas dessa från att exponeras i loggar och användargränssnittet.
Den här templaten tillhandahåller ett stabilt och säkert sätt att driftsätta en WordPress-applikation med skyddade resurser i AWS.

## Förslag på åtgärder för ökad säkerhet:
- Just nu är port 22 öppen för alla IP-adresser (0.0.0.0/0) på flera säkerhetsgrupper, vilket ökar risken för brute-force-attacker. För att säkra detta rekommenderas det att begränsa åtkomst till specifika IP-intervall, till exempel en specifik bastion-server eller ett känt IP-intervall.
- RDS-databasen och EFS är tillgänglig från alla IP-adresser via port 3306 och 2249, vilket kan vara en stor säkerhetsrisk. Begränsa åtkomst till RDS via enbart säkerhetsgrupper för interna EC2-instanser som behöver åtkomst.
- Använd HTTPS istället för HTTP med ett SSL-certifikat på Lastbalanseraren.
- Skapa specifika IAM roller med minimal åtkomst för EC2 istället för att lagra access-nycklar på EC2-instanserna.
