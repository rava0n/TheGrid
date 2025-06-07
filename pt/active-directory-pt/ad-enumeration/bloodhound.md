# BloodHound

BloodHound is a single page Javascript web application. It uses graph theory to reveal the hidden and often unintended relationships within an Active Directory or Azure environment.\
Attackers can use BloodHound to easily identify highly complex attack paths that would otherwise be impossibile to quickly identify.&#x20;

{% embed url="https://github.com/SpecterOps/BloodHound" %}

## Installation on Kali

```bash
sudo apt-get install bloodhound
```

Part of the installation process, neo4j database management solution that is required for BloodHound will also be installed that will need to be configured.

## Configuring BloodHound

Once the installation is complete, we need to configure neo4j - mainly just change default passwords, so let's run:

```bash
neo4j console
```

<figure><img src="../../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

and navigate to [http://localhost:7474/](http://localhost:7474/) to set up a DB user account by changing default passwords from **neo4j:neo4j** to something else - we will need those credentials when launching BloodHound itself.

<figure><img src="../../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

## Running BloodHound

```bash
bloodhound
```

Login with your previously set credentials from neo4j:

<figure><img src="../../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>



## Ingesters

### Use bloodhound.py

Use this ingester to collect all AD data from a domain controller. (we use docker container to make sure that all dependences are installed)

{% code title="Kali python tool" overflow="wrap" %}
```bash
sudo apt install bloodhound.py

bloodhound-python -c all -ns $TARGET_IP -dc $DC_NAME -d $DOMAIN_NAME --zip -u '$USER' -p '$PASS'
```
{% endcode %}

{% code title="With Docker" overflow="wrap" %}
```bash
git clone https://github.com/dirkjanm/BloodHound.py -b bloodhound-ce
# -b: specific the branch

chown kali:kali -R BloodHound.py && cd BloodHound.py
docker build . -t bloodhound-ingester

docker images
docker run -it --rm $IMG_ID sh # maybe: -v $(pwd):/mnt/ sh

# run command into docker envirnment
python3 bloodhound.py -c all -ns $TARGET_IP -dc $DC_NAME -d $DOMAIN_NAME --zip -u '$USER' -p '$PASS'

# to copy output file from kali
docker cp $CONTAINER_ID:/bloodhound-data/$OUTPUT_FILE .
```
{% endcode %}

Now that we have a BloodHound output zip file, upload it to BloodHound Web application hosted on port 8080 of localhost

### Use SharpHound

```bash
git clone https://github.com/BloodHoundAD/BloodHound
cd BloodHound\Collectors
```

```powershell
# import script
powershell -ep bypass
. .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All # all the data will be stored as a zip file
```

```bash
# use .exe into target machine
upload SharpHound.exe # put file with evil-winrm
.\SharpHound.exe

# download the result from evil-winrm
download 20221217070128_BloodHound.zip
```

Upload this file in BloodHound application to get a graphs.

## Upload BloodHound results

Once generated bloodhound or sharphound results, upload the .zip output file in the BloodHound CE.

<figure><img src="../../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

## Generate report with Plumhound

PlumHound allow us to have a report with all bloodhound datas.

{% hint style="warning" %}
To generate a report, we have already start Neo4j, Bloodhound and load some datas
{% endhint %}

{% embed url="https://github.com/PlumHound/PlumHound" %}

{% code title="Installation" %}
```bash
cd /opt
sudo git clone https://github.com/PlumHound/PlumHound.git

cd PlumHound
sudo pip3 install -r requirements.txt
```
{% endcode %}

Once we have all installed, let's generate the report.

```bash
sudo python3 PlumHound.py -x tasks/default.tasks -p NEO4J_PASS
```

Then, read the report

```bash
cd reports
firefox index.html
```

## Usefull query

### Find all Domain Admins

This query allow us to retrieves all Domain Admin accounts in the Domain.&#x20;

### List all Kerberoastable Accounts

This query allow us to recover all Accounts vulnerable to the Kerberoasting attack

### Find AS-REP roastable Users (ContreqPreAuth)

This query allow us to recover all user accounts vulnerable to the AS-REP roasting attack (with pre authentication disable).

### Find Principals with DCSync Rights

This query allow us to recover all user accounts that has rights to do a DCSync.&#x20;

What is a DCsync???

### Find Kerberoastable Members of High Value Groups

This query allow us to find all Kerberoastable Members of hugh value groups. For example a kerberoastable user in the "_Account Operators_".

### Search a Specific Group/User

we can search a specifica group/user and retrive all members.

<figure><img src="../../../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

* Click on the "_Direct Members_" and view all **member of the group**.

&#x20;

<figure><img src="../../../.gitbook/assets/image (124).png" alt="" width="361"><figcaption></figcaption></figure>

* Click on the "_First Degree Group memberships_" or "_Unrolled Group Memberships_" to view all group memberships of the searched user.

<figure><img src="../../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

### Get all GPO

Type "GPO:" in the search bar to view the available GPO's.

<figure><img src="../../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

### Find the shortest path to Domain Admins from a Kerberoastable specific user

#### First method

Click on the **Pathfinding** icon on the left menu. Type and select the **account name** as the start node and then type and select Domain Admins as the target node. Run it.

<figure><img src="../../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

#### Second method

Another method is to first search for the **account name**. Now, let's say you have compromised the account. You can mark this user as compromised or owned by right clicking on the node and selecting **Mark User as Owned**.

<figure><img src="../../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

A little skull icon will appear on the node indicating that you have control over this object. After that, you can go to **Analysis** tab and run the **Shortest Paths to Domain Admins from Owned Principals** query.

<figure><img src="../../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

### **Find all the reachable high value targets from a group.**

Search for the group and then click on the node. Now, simply click on **Reachable High Value Targets** in the **Overview** section under **Node info**.

<figure><img src="../../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

### **List the outbound object control for a specific user**

Search for a specific user and then click on the user node. On the **Node info** tab, scroll down to the **Outbound Object Control** section.

<figure><img src="../../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

We have three types of outbound object control:

* **First Degree Object Control:** The number of objects in AD where this user is listed as the IdentityReference on an abusable ACE. In other words, the number of objects in Active Directory that this user can take control of, without relying on security group delegation.
* **Group Delegated Object Control:** The number of objects in AD where this user has control via security group delegation, regardless of how deep those group nestings may go.
* **Transitive Object Control:** The number of objects this user can gain control of by performing ACL-only based attacks in Active Directory. In other words, the maximum number of objects the user can gain control of without needing to pivot to any other system in the network, just by manipulating objects in the directory.

**Source:** https://bloodhound.readthedocs.io/en/latest/data-analysis/nodes.html#outbound-object-control
