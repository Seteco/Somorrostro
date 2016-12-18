![image8](https://cloud.githubusercontent.com/assets/2662304/14253735/536f4580-fa95-11e5-9f7b-99112b31a5d7.gif)

# So, your netdata is outdated?

We suggest to keep your netdata updated. We are actively developing it and you should always update to the latest version.

The update procedure depends on how you installed it:

## You downloaded it from github using git

The installer should have created a `netdata-updater.sh` script in the directory you downloaded. Just run it and it will download and install the latest version. The same script can be put in a cronjob to update your netdata at regular intervals.

If you don't have this script (e.g. you deleted the directory where you downloaded netdata), just follow the **[[Installation]]** instructions again. The installers preserves your configuration.

Keep in mind, netdata may now have new features, or certain old features may now behave differently. So pay some attention to it after updating.

## You downloaded a binary package

If you installed it from a binary package, the best way is to **obtain a newer copy** from the source you got it in the first place.

If a newer version of netdata is not available from the source you got it, we suggest to uninstall the version you have and follow the **[[Installation]]** instructions for installing a fresh version of netdata.

---

(This page is used to by the netdata dashboard to help the users update)
