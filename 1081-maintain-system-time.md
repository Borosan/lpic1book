# 108.1. Maintain system time

## **108.1 Maintain system time**

**Weight:** 3

**Description:** Candidates should be able to properly maintain the system time and synchronize the clock via NTP.

**Key Knowledge Areas:**

* Set the system date and time
* Set the hardware clock to the correct time in UTC
* Configure the correct timezone
* Basic NTP configuration
* Knowledge of using the pool.ntp.org service
* Awareness of the ntpq command

**Terms and Utilities:**

* /usr/share/zoneinfo/
* /etc/timezone
* /etc/localtime
* /etc/ntp.conf
* date
* hwclock
* ntpd
* ntpdate
* pool.ntp.org

When we install a Linux® system graphically, we set the clock and choose a time zone suitable for our needs ,we can also choose to use the Network Time Protocol \(NTP\) to set your clock automatically. In this lesson we show  how to go below the graphical interfaces and configure the various time-related aspects of Linux system.

#### How Linux keep tack of time

 there are 2 clocks in each computer. The first is the Hardware Clock. This is the clock on a motherboard chip that keeps time even when the machine is powered off. 

The other clock is the virtual System Clock. Linux asks the Hardware Clock chip what time it is on power up and then keeps track of the time itself with software. 

Hardware clock can be the localtime \(your computers timezone\) or UTC time \(standard time\). 

> We can determine which one is set by checking /etc/adjtime .

Usually the hardware clock is set on UTC , so when ever system boots up, Software clock reads Hardware clock and then calculates the difference based on our timezone.

#### Setting The System Clock

### date









hardware clock vs system clock

/etc/adjtime

local time or utc time



date  date -s "01/02/2015 12:20:34"  -&gt; system clock

hwclock

it doesn't tell local or utc. it always display local time weither local e ya utc

cat /etc/adj

sudo hwclock --set --date="02/12/2020 20:20"

cat /etc/adjtime

hwclock --localtine --set --date=""

cat /etc/adjtime

switch back

hwclock -u -w \(w:sysn system time to hardware clock\)

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-1/](https://developer.ibm.com/tutorials/l-lpic1-108-1/)

[http://xed.ch/help/time.html](http://xed.ch/help/time.html)

.

