# Pivoting Environment Lab with Ligolo-MP

## Environment setup

| Machine          | Role             | Info                                                   |
| ---------------- | ---------------- | ------------------------------------------------------ |
| Kali 1           | Attacker machine | Your main system running Ligol-MP Proxy                |
| Kali 2           | Pivot Machine    | Runs Ligolo-MP Agent and hosts the Docker target.      |
| Docker Container | Target Machine   | Inside the Pivot Machine, simulating an internal host. |



## Step 1: Set Up the Network with Docker

We need to isolate the **Docker container** from the **Attacker Machine** so it can only be accessed through the **Pivot Machine**.

We'll assign the Docker container to a custom bridge network inside the Pivot Machine.

```bash
docker network create --subnet='192.168.100.0/24' internal_net

sudo docker network ls # check if the network has been created
```

## Running the container in the Pivot Machine

Then, when running the container:

{% code overflow="wrap" %}
```bash
docker run --rm -it --network internal_net --ip 192.168.100.100 --name target_box vulnerables/web-dvwa
```
{% endcode %}

If everythings is going well, we would see this page to the `http://192.168.100.100` URL from Pivot Machine:

<figure><img src="../../../.gitbook/assets/image (201).png" alt=""><figcaption></figcaption></figure>

## Run the Payload to Pivot machine.

start ligolo

```
sudo systemctl start ligolo-mp.service
sudo ligolo-mp
```

Create a Pivot payload and run it into pivot machine

[#craft-the-payload](ligolo-mp.md#craft-the-payload "mention")



Start the Relay from the ligolo dashboard and view the onther interfaces.

<figure><img src="../../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

We are in the eth0 network and we want reach the network 192.168.100.1/24

## Create the Route

Create the Route to reach the other network.

<figure><img src="../../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

Now we can ping the target machine from Kali1 machine.

<figure><img src="../../../.gitbook/assets/image (206).png" alt=""><figcaption></figcaption></figure>



