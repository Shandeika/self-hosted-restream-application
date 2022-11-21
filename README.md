# Self Hosted Alternative to Restream Application

Restream, Castr.io are some of the most popular multi streaming applications that let you stream from one source to multiple locations such as Facebook, Twitch, YouTube, Twitter and so on.

However, license fees tagged to these applications are high and as a result, if you don't have a budget for one of the highly priced licenses, this Self hosted Nginx-RTMP based application is good for you and will offer you the best multi streaming options.

In cases where you can't stream to an application directly, you can stream to a Free Restrem or Castr application to take care of that.

This applicaition is production-ready and can safely be deployed on any of your running server instances without any critical runtime errors.

### Cost Comparison with Restream, Castr, and other multi-streaming platforms

|     | Self Hosted | Restream | Castr |
| --- | --- | --- | --- |
| PRICE: | $20 (4GB RAM, 2VCPus Server) | $294 per year, Proffesional License | $450 per year, All in one - Entry License |
| Streaming Channels: | Unlimited Platforms (based on server power) | Upto 8 Platforms | Upto 10 Platforms |
| RECORDING: | Unlimited (Based on Server Storage) | upto 10 hours | upto 72hours |

## Prerequistes

To use this Self Hosted Alternative to Restream. You need.

* Some basic Skills on using the Linux Shell.
* Some basic Skills on using Docker.
* A Linux Virtual Private Server (VPS).
  > You can deploy one with any of the trusted providers such as DigitalOcean, Vultr, Linode, or Hetzner cloud. For this application, I used a production-ready Docker One Click Applicaiton from the Vultr Marketplace.
* Install Git on the server (pre-installed on most Linux distributions).
* Setup your stream on the target platform to get a Stream URL and unique Stream Key.
  > In the preset example variables, Twitch and Youtube stream keys are used. If you have any other platforms, you can edit the environment file and include the associated keys.

## Installation

1. Use SSH to securely Login to your server.

         $ ssh root@YOUR-SERVER-IP

2. For easy setup, switch to your user home directory.

         $ cd ~/

3. Clone into this repository using the `git` utility.

         $ git clone https://github.com/mjhumphrey4/self-hosted-restream-application

4. List all directories to verify that a new `selfhosted-restream` directory is added.

         $ ls

5. Switch to the new directory.

         $ cd /self-hosted-restream-application

6. Rename the environment `.env.example` file to `.env`.

         $ mv .env.example .env

7. Using a text editor like Nano or VIM, edit the `.env` file.

         $ nano .env

8. Edit the TWITCH, and YouTube variables to represent your Stream keys as below.

         STREAM_KEY_TWITCH=stream-key
         STREAM_KEY_YOUTUBE=stream-key

   > If you have any other target platform. Please define it in this file.

   Save and close the file.

9. Open the `restream-app.yml` Docker Compose file.

         $ nano restream-app.yml

     Verify that your variables are correctly referenced to within the `environment` section.

         environment: 
           - STREAM_KEY_TWITCH
           - STREAM_KEY_YOUTUBE

    > If you didn't define any new target platform variables, leave the file as it is. Else, be sure to declare your platform as defined in the `.env` file.

10. Edit the `nginx.conf` file.

         $ nano nginx.conf

11. Enter your platform's `rtmp` URL within the `application mystream {` section in the following format.

         push rtmp://YOUR-PLATFORM/YOUR-STREAM-KEY

    By default the Twitch and YouTube URLs are used, simply verify that they correctly match with your URL and `.env` variable name as below.

         push rtmp://live.twitch.tv/app/${STREAM_KEY_TWITCH};
         push rtmp://a.rtmp.youtube.com/live2/${STREAM_KEY_YOUTUBE};

    To add a new RTMP URL for any other platform, add it in the `push rtmp://Stream-URL-here` format, then add its variable just after `/` in the format `${STREAM_KEY_YOUR-PLATFORM};` as defined in the `.env` file. Be sure to maintain the `;` termination, else, Nginx will throw errors in the container.

    Save and close the file once you have made any changes.

12. Deploy the application using Docker Compose as below.

         $ sudo docker-compose -f restream-app.yml up -d 

13. Run `docker-ps` to verify that the container is up and running.

         $ docker ps

## Setup your Streaming Software (OBS, Wirecast, Vmix, or Streamlabs Desktop (OBS))

This section uses the Open Broadcastign Software (OBS). If you are running any other desktop streaming software such as Vmix, Wirecast, or Streamlabs OBS, follow the same instructions below to setup your custom RTMP Link.

1. Open OBS (or your streaming software) from your computer's start or applications menu.
2. Open **Setting**, and Go to the **Stream** section.
3. Toggle the **Stream Platform** list and select **Custom** from the list.
4. Enter your RTMP Server URL, Port, and service. In case you are running a reverse proxy such as `Nginx` or `Caddy` on the server's host side, simply enter your domain name. Your Server URL and Port should be similar to the one below.

         rtmp://PUBLIC-SERVER_IP:1935/mystream

   > Please replace **PUBLIC-SERVER-IP** with your actual server IP Address, and **mystream** with the actual RTMP application name you used in your `nginx.conf` file.
   > If you are running this setup in a local network, please enter the target server's IP Address.

5. Click **Apply**, then click **OK** to save your stream settings.
6. Now, setup your OBS Stream sources, and once ready to stream, click **Start Streaming** to begin the stream.

> OBS Should connect immediately and a Green (Stream Okay) sign should display within the stream information timer and bitrate details. If OBS returns a `failed to connect to the server` error, please open port `1935` on the server firewall and verify that the application is running in Docker. To Allow the port `1935` through the default Ubuntu firewall, simply run the following command.

         $ sudo ufw allow 1935/tcp

     For CENTOS use `firewalld` to allow the port.

## Test

1. Go to your Twitch profile, your stream should be displaying.
2. Go to your YouTube LiveStream Dashboard, verify that the stream is showing and click **Start Stream** to publish it to your subscribers.
3. Incase one of your target platforms is not receiving stream feed, please view the application logs using the following command.

         $ sudo docker logs self-hosted-restream

> Analyze the listed error, and if possible, verify that you set the correct stream key in the `.env` file. Then, also verify that you declared the Platform's variable in the `restream-app.yml` file.

1. To view the incoming streaming feed inside your browser window other than your target platforms, simply visit your Server IP using the RTMP protocol URL as below.

         rtmp://YOUR-SERVER-IP:1935/mystream

## Conclusion

That's it, you have deployed a working self hosted alternative to Restream. Incase of any suggestions to this application, please raise an issue as I will be actively maintaining this repository.

CREDIT:

> This application is a Fork from [Rafalfaro18](https://github.com/rafalfaro18/self-hosted-restream) and uses the original Docker image from his repository. In future, I may provide a different docker image, for now, all credit goes out to the original author.