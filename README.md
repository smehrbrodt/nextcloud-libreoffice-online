# Nextcloud with LibreOffice Online

This docker configuration help with setting up Nextcloud with LibreOffice Online
for testing purposes.
It keeps things as simple as possible (no https e.g.).

## Preparations

To get started, you need two virtual machines with two different hostnames.
Install some recent Linux distro on them, e.g. Ubuntu 18.04 (64bit required).

Install docker and docker-compose on them.

Then clone this repo. You need the folder `nextcloud` on one machine and
the folder `libreoffice-online` on the other machine.

## Setup Nextcloud

Go to the folder `nextcloud`. If you want to set up nextcloud with a MariaDB database, copy the `.env.sample` to `.env` and set the environment variables accordingly.
Note that the database host is called 'db'.

If you want to use sqlite instead, you can skip this step.

Run `docker-compose up -d`.
This will setup Nextcloud and the database for you.

Now configure Nextcloud in your web browser. It's available on http://yourhost:80
You can change the port in docker-compose.yml.

When nextcloud is set up, install the App "Collabora Online". Then go to Configuration->Collabora Online
and enter the domain name of your other VM, e.g. http://libreoffice.yourhost:9980

## Setup LibreOffice Online

On your other VM, go to the folder "libreoffice-online".
Copy the `.env.sample` to `.env` and set the environment variables accordingly.
NEXTCLOUD_DOMAIN must be set to the domain where your nextcloud is running, e.g. `yourhost` in this example.
LO_ONLINE_USERNAME and LO_ONLINE_PASSWORD are used to access the LibreOffice Online dashboard (not absolutely necessary).

Once set, run `docker-compose up -d`.
This will start LibreOffice online. It uses SSL by default and we need to disable that.

Find out the ID of your running docker container by typing `docker container list`.
Then get into your docker container with `docker exec -it <container-id> bash`.

Now edit /etc/loolwsd/loolwsd.xml and disable SSL (section <ssl><enable>). You might have to install an editor beforehand.

Leave your container, then restart it:
```bash
docker-compose stop
docker-compose start
```

After that, your LibreOffice online container should be accessible with http.

## Using it

You should now be able to edit documents in your nextcloud with LibreOffice online.
Create a document using the '+' button or upload some.

To access the LibreOffice Online admin dashboard, visit http://libreoffice.yourhost:9980/loleaflet/dist/admin/admin.html and use the credentials from your .env file.

## Troubleshooting

If it doesn't work, look at the logs from your docker containers, easiest with:
`docker-compose logs`
