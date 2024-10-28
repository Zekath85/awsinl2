# AWS Inlämningsuppgift 2
### Skapa en robust och säker och eventuellt skalbar wordpress-site som utnyttjar EFS (Elastic File System) och RDS (Relational Database Service).

Denna uppgift är löst med hjälp av Cloudformations och ett bash-script. Genom att använda CloudFormation kan hela infrastrukturen skapas automatiskt och konsekvent, vilket minskar risken för mänskliga fel och säkerställer att samma konfiguration används i varje distribution.

Infrastuktur:
![image](https://github.com/user-attachments/assets/053cbb37-bda8-412a-bc4b-eb4d7c242bb6)
__

2. VPC och subnät
Skapar en VPC med CIDR-blocket 10.0.0.0/16 och tre offentliga subnät.
Säkerhetsfördel: Begränsar resurserna till specifika IP-intervall inom VPC.

4. Internet Gateway och routetabell
Skapar och kopplar en Internet Gateway för att möjliggöra internetåtkomst för VPC
och lägger till en routetabell för att dirigera trafik till internet. Subnät är kopplade till denna routetabell.
Säkerhetsfördel: Endast specifika subnät har internetåtkomst, vilket förbättrar nätverkssäkerheten.

5. Säkerhetsgrupper
Skapar olika säkerhetsgrupper för att hantera trafik:
ALB-säkerhetsgrupp: Tillåter HTTP-trafik (port 80).
VmSecurityGroup: Tillåter HTTP (80), SSH (22), och RDS-trafik (3306).
ProvisioningServerSecurityGroup: Tillåter SSH och EFS-åtkomst.
RDSSecurityGroup: Begränsar RDS-åtkomst (port 3306).
EFSSecurityGroup: Tillåter EFS-åtkomst (port 2049).
Säkerhetsfördel: Begränsad åtkomst till specifika portar och protokoll ökar säkerheten genom att bara tillåta nödvändig trafik.

6. Application Load Balancer (ALB)
Skapar en ALB som hanterar HTTP-trafik och dirigerar den till de instanser som tillhör en specifik Target Group.
Säkerhetsfördel: Lastbalanseraren minskar exponeringen av backend-resurser och kan också ge skydd mot DDoS-attacker.

7. EFS (Elastic File System)
Skapar ett EFS och mount-punkter i varje offentligt subnät för att hantera delad filsystem-åtkomst.
Säkerhetsfördel: EFS är krypterad för att skydda data.

8. RDS (Managed Database)
Skapar en RDS-databas (MariaDB) för att lagra data och ansluts till en Subnet Group. PubliclyAccessible är satt till false för att förhindra direkt åtkomst från internet.
Säkerhetsfördel: Endast tillgänglig från specifika IP-adresser inom VPC
och krypterar datatrafik.

9. EC2-instans för Provisioning Server
Skapar en EC2-instans för provisioning med user data script för att installera och konfigurera en WordPress-installation.
Säkerhetsfördel: Instansens säkerhetsgrupp begränsar trafik och tillåter endast SSH, vilket säkrar instansen från otillåten åtkomst.

10. Auto Scaling Group (ASG)
Skapar en ASG med en startmall för att hantera skalbarhet och upprätthålla flera instanser för applikationen.
Säkerhetsfördel: Kontinuerlig drift och redundans ökar applikationens motståndskraft vid attacker eller hårdvarufel.
Säkerhetsfördelar totalt sett
Segmentering av nätverkstrafik (subnät och säkerhetsgrupper) begränsar exponeringen för externa hot.
Kryptering av EFS och begränsad åtkomst till RDS skyddar datalagring och överföring.
Begränsade säkerhetsgruppsregler skyddar applikationens komponenter och följer principen om minsta nödvändiga åtkomst.

Förslag på åtgärder för ökad säkerhet:
Bastion Host för SSH behörighet
Använd HTTPS istället för HTTP med ett SSL-certifikat på Lastbalanseraren.
Skapa specifika IAM roller med minimal åtkomst för EC2 istället för att lagra access-nycklar på EC2-instanserna.
