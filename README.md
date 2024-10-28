# AWS Inlämningsuppgift 2
### Skapa en robust och säker och eventuellt skalbar wordpress-site som utnyttjar EFS (Elastic File System) och RDS (Relational Database Service).

Denna uppgift är löst med hjälp av Cloudformations och ett bash-script. Genom att använda CloudFormation kan hela infrastrukturen skapas automatiskt och konsekvent, vilket minskar risken för mänskliga fel och säkerställer att samma konfiguration används i varje distribution.

![image](https://github.com/user-attachments/assets/053cbb37-bda8-412a-bc4b-eb4d7c242bb6)__
## Säkerhet:

## Hög tillgänglighet och skalbarhet:

## Förklaring av delar:




Sammanfattningsvis ger detta skript både säkerhet genom isolering av resurser och skalbarhet genom automatiserad hantering av resurser, samtidigt som det säkerställer hög tillgänglighet genom användning av flera tillgänglighet zoner och en lastbalanserare.

Förslag på åtgärder för ökad säkerhet:
Bastion Host för SSH behörighet
Använd HTTPS istället för HTTP med ett SSL-certifikat på Lastbalanseraren.
Skapa specifika IAM roller med minimal åtkomst för EC2 istället för att lagra access-nycklar på EC2-instanserna.
