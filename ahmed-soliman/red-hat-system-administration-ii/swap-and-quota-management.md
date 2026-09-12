# Swap and Quota Management

#### **Swap**&#x20;

**Swap Memory Explained**

First, the term **swap** means _exchange_.&#x20;

The reason we have swap is that your **RAM can get full** when you have many applications running.

Will all applications stay active and running at the same time? **No.** It’s impossible for all applications to run simultaneously.

Applications that are **not currently running**, **suspended**, **temporarily stopped**, or **waiting for I/O** (input/output) don’t need immediate access to RAM. These applications are moved to **swap** — a reserved space on the hard disk.

When the user wants to interact with other applications or start new ones, the system **brings the necessary apps back from swap to RAM**. This back-and-forth process is why it’s called **swap**: it exchanges data between RAM and disk as needed.

Who performs this process? **You don’t.** The **operating system (OS) or kernel** manages it automatically.

You need to know that the kernel will use the swap space on the hard disk when RAM is full.&#x20;

Of course, using swap is slower than RAM, so the system will also slow down.&#x20;

Naturally, RAM speed is much faster than the hard disk, and therefore performance will be slower.&#x20;

You can’t compare RAM speed with a Mechanical Hard Disk, and SSD does not always replace RAM. SSD is much faster than a hard disk, but you might not need swap at all if you always have enough RAM.

Regarding swap size, there is no strict standard. It depends on how many apps you will run.&#x20;

You need proper planning for swap space because it is the space you reserve on the hard disk for swap. The word “Planning” means you need to check the Memory Requirements of the applications you will run.&#x20;

You can see the minimum, recommended, and best use values, of course, leaving space for other apps.

Remember, having little swap is better for performance.&#x20;

Keep an eye on it when you run commands like `free -m`. Don’t focus too much on `free`; just observe.

Take your cash into account too. Why does it stay? Because you may have free space that is small, but most of the apps in the cache take from the memory cache.\
So what does the cache do?

Because if you do not want to use swap, then you should start to worry if the cache space becomes small and the free space becomes small too. You should check the used space. If you find it full, then start to worry.

Keep in mind that the **/proc** directory has everything the kernel knows about you, whether it is services, physical components, or the kernel itself. Anything special in the device is found in **/proc**.

So if you want to increase the swap space, you have two ways:\
Either you make a partition and make it swap, and add it to the existing swap space — this is the second space — or the second way is through a file.

And do not forget, after you make the swap, you put it in the **fstab** so that it stays after reboot. And of course, you must make sure you run **mount** every time. So keep running this command:

**mount -a**

s long as no error appears, then everything is fine.\
So if you want to deactivate the swap, the first thing is to make sure you remove it from **fstab**.

A note about the machine: the word **format** means **zero fill**, meaning it fills the blocks with zeros.

Now the second method in the swap process is that you make a file using the **dd** command, and this file will be made of blocks all filled with zeros, through this command:

**dd if=/dev/zero of=/root/myswap bs=1M count=2048**

After we create the file, we will make it a file system.\
How do we do that? We said before that the file is made of zeros. So we must make a file system for it with the **format** command, or you can say it will be on a **raw file system**, meaning a partition is not yet on it, and then you write:

**mkswap /root/myswap**

And then we activate the swap through:

**swapon /dev/myswap**if this message appears to you:

**swapon: /root/myswap: insecure permissions 0644, 0600 suggested**

It means it is warning you and telling you that **others** have **read access** to this file through permission number **0644**, and it suggests that you prevent **others** from having any access to this file.\
So you will stop the swap for a moment:

**swapoff /root/myswap**

Then you change the permissions of this file:

**chmod 600 /root/myswap**

And after that, you activate the swap again:

**swapon /root/myswap**

Of course, this is because it prevents **users** from having any direct access to the memory of the device.\
And do not forget to save all of this in **fstab**, and after that:

**mount -a**

And this way, you can make the swap either on a **file** or on a **partition**.

**Quota**

The word **"Quota"** in Arabic means "share" or "portion." Here, it means the specific amount of **storage** given to each user. By the way, **Quota** is only concerned with **Storage**—meaning the space that users will take—but it is **not** concerned with the **performance** of the storage itself. For example, it cares about giving a certain user 10 GB, but it does not care about how fast they can write to the disk. So, in short:\
**Quota** is concerned with **space**, while **Cgroup** is what determines **how fast** they can write.

Now, to **apply Quota**, when you **mount** the file system, you must tell it that you will use Quota. This means the **file system** you are mounting must **support** it.\
The next step is that you must **enable** Quota.

Now, here is a question you should ask: How does Quota know if a user has used 1 GB or 2 GB or any amount?\
The answer is that when Quota is working, there is a **database** that contains information about the users and how much space they have used.

Imagine you have a **File System** that was created, but **Quota** was not enabled on it from the beginning. This means that the **Quota Database** does not exist. That also means when you want to enable Quota, you must first go and **create** this database.

In another situation, imagine that Quota was **working** on this File System, meaning it was **ON**. Then you turned it **OFF**. After that, users came and wrote files on this File System. Is the Quota information now **up to date**? No, of course not. And then you are forced to **recreate** the Quota.

So, you have **two cases** where you are forced to **create** the Quota. The first is when the file system **did not support Quota**, and then you enabled it. The second case is when it was working, and you turned it **OFF**, and then users created a lot of files on that file system. In that case, you should **recreate** the Quota.

The command **`lsof`** shows you the files that are **open** from a specific location. For example:\
`lsof /usr/`

In other words, it shows you what is being **used** from that specific place, which might be preventing you from unmounting it.

Alright, before it becomes enabled, while you are mounting the file system?

The answer is no, of course not.

Now we come to the topic of activating Quota while you are mounting it, using this command:\
`mount -o usrquota,grpquota /dev/data/mssql /google`

Or by using this one, if it works, meaning:\
`udisksctl mount –block-device /dev/data/mssql -o usrquota,grpquota`

Now, we see here `usrquota` and `grpquota`. This is because simply, Quota can be applied to a single user, or to a group of users in a specific group. This means you have a directory with a space of 50 GB. So, everyone in this specific group will be able to write in this directory. Now, if all of them are writing, then all of them share the 50 GB together. This means they will all reach the same limit together.\
Alright, let's tell the kernel to enable Quota. This is done using the command:

**`quotaon /google`**

Look at the **Warning** message:

**`quotaon: cannot find /google/aquota.group on /dev/mapper/data-mssql [/google]`**

**`quotaon: cannot find /google/aquota.user on /dev/mapper/data-mssql [/google]`**

This means the command did not find the database for the user or for Quota. This means you must first create the database for the group. You can do this using the command:

**`quotacheck -cvug /google`**

**`c` —> create**

`v` --> verbose\
`u` --> user\
`g` --> group

After that, you go back and do:

`quotaon /google`

Alright, now let's implement it. In principle, you can implement it either by **Size** or by **Number Of Files**.\
This means I can say that the user named Ahmed, for example, is only allowed to write **5 GB**, or he is only allowed to write **5 files**.\
Now, do I need to tell him to write a specific number of files??\
No, of course not. Now, why? Because simply, the topic is related to **inodes**. Let me remind you that in every file system you have a specific number of inodes. If they finish, then your partition's space is effectively finished, just like that.

Wait a moment. Also, look at it simply: if you have a user and you give them a space of 1 GB, and they go and create about a million empty files, then they will use up all the inodes you have. Do not forget that each inode points to a file. This will cause you to have free space, but you cannot write anymore. This is because the number of inodes is finished, and this way you lose the storage you have.

The second point when you are implementing Quota is that you have 2 limits. You either have a **soft limit** or you have a **hard limit**. Alright, what are these two?

Look, the **soft limit** is the limit that, when the user reaches it, will give them a **warning**. On the other hand, the **hard limit** is the limit that the user can **never** pass under any circumstances.

Example: For instance, you have an email account with 10 GB, right? The warning will appear for you when you use 9 GB, and that is the **soft limit**.\
But the 10 GB is the **Hard Limit**. Now, is it possible for you to pass the soft limit? Yes, you can pass it until you reach the hard limit.

Alright, now let's do the implementation of Quota for the user named Ahmed, using the command:

Look at these few lines:

**Disk quotas for user rhadmin (uid 1000):**

| Filesystem             | blocks | soft | hard | Inodes | soft | hard |
| ---------------------- | ------ | ---- | ---- | ------ | ---- | ---- |
| /dev/mapper/data-mssql | 0      | 0    | 0    | 0      | 0    | 0    |

Of course, you ask: why didn't it bring the other partitions that are mounted?\
The answer is simply because Quota is not enabled on them.\
Alright, the **blocks** are the actual number of blocks that this user is using.\
The number 0 means he is not using anything at the moment. Now, the question again: when you implement Quota, will you change these blocks? Of course not, because these blocks represent the space the user is currently using. So, you are supposed to change the **soft limit** and the **hard limit**.

This means you can set the soft limit to a number, for example **100000**. This means 100 Mega, or it means 100 Kilobytes which is also 100 Mega. And also, for example, set the hard limit to **200000**. This way, you will implement the Quota based on **size**.

Alright, after you have implemented Quota for the user named Ahmed, and you execute the command:

**`repquota -a`**

You will find the output in this form:

eport for user quotas on device /dev/mapper/data-mssql\*\*

**Block grace time: 7days; Inode grace time: 7days**

| User | Block limits | File limits |      |       |      |      |      |       |
| ---- | ------------ | ----------- | ---- | ----- | ---- | ---- | ---- | ----- |
|      | used         | soft        | hard | grace | used | soft | hard | grace |
| root | -            | 20          | 0    | 0     | 2    | 0    | 0    |       |

Alright, why is the user named Ahmed not appearing? I will tell you simply. It is because the directory named `google`, or more accurately, the partition named `/dev/data/mssql` which was mounted in the folder named `/google`, did not have any write permissions for **others**. Now, let's execute this command to know if **others** have permissions or not:

`ls -ld /google/`

`drwxr-xr-x. 3 root root 4096 jun 13 22:33 /google/`

\
ow you must give **write** permissions to **others**, of course, as you know, using:

`chmod o+w /google/`

If you execute the `repquota` command again, it will show you the report for the Quota for the user named Ahmed. So, it will appear when the user starts to use any files on the File System.

(Note this point: if, while setting up the distribution, you created a user named Mostafa for example, and you clicked on **"make this user as administrator"**, then this means that this user is like the root user. This means when you apply Quota, it will not affect him because he is under the root, not under it. So, be careful that Quota should be applied to ordinary users, not to **administrators**.)

Now look at this line:

`ahmed + 122880 100000 200000 6days 2 0 0`

First, the **+** symbol refers to the **blocks**, and when it appears it means that the user has passed the **block limit**.

On the other hand, the **-** symbol means that he has **not** passed the allowed **file limit**. Here, it appears as **-** because no limit for the number of files was set for him. So, to be clearer, the **+** means he has passed his **soft limit**.

Now, let's set a limit for the files that the user is allowed to create, also using the command:

`edquota -u ahmed`

And you will change the last **soft** and last **hard** after the word **inode**, and of course you can also use `watch` with `repquota`.

By the way, if you want to implement a quota for a **group**, for example the group named `academics`, you do it using `edquota -g academics` in the same way as above. Look for the group name and so on, and also `repquota -g /google`.

And now, also, you can make a quota for a **specific user** inside a specific group. But first you must make the quota for the **group**, and then go back and make the quota for that user.

By the way, if you want to get the Quota for a specific user, you can use this command:

`quota -u ahmed`

Now, what is the story of this **grace period**? Look, in short, it is the period during which the system will treat the user who exceeded his quota as if he reached the **hard limit**.

For example, you reached the **soft limit**. Then, for example, the **grace period**, which is the allowed period here, starts. It will start treating you as if you reached the soft limit after, for example, 6 days. This is in case you did not delete any of your files.

Now, suppose you want to edit the **grace period**. It is very simple. Write:

`edquota -t grace`            &#x20;

And in this file, you can either edit the grace period for the **Size** of the files, which are the **Blocks**, or you can edit the **Inodes**. And of course, the edit can be in days, hours, or minutes.

But note that you have not specified any user here. So, for the **grace period** topic, you must specify which user exactly. This is done using the command:

**`edquota -T ahmed`**

Finally, if you want to **stop** Quota, it is very easy. Just execute this command:

**`quotaoff /google`**

And then, any user can write again.

Now, here is another good question. Now, if I stopped Quota and a user wrote files, and then you started Quota again, will Quota be able to read what was newly written?

Certainly not, of course. Because simply, the **Quota Database** is not **updated**.

**Important note: before you do any check for Quota, make sure Quota is stopped first.**

**`quotaoff /google`**

And then:

**`quotacheck -vugu`**\
However, if you updated the database on the fly... and I tell you that if you do this, the database for Quota might become corrupted.

* It could become corrupt.
* It will behave incorrectly, like playing with the steering wheel while the car is moving. That is not logical at all.

In the same context, you will never see the **used** amount greater than the **hard limit**. Except in one case. That is when Quota is working, then you stop it, then you write on the mounted partition, and then you go and **update** the Quota database, and then you start Quota again. At that time, you might find that the **used** amount is greater than the **hard limit**.

here is an important point here. If you mounted the partition, and then you execute the command `lsblk` and find that the partition is still not mounted, then you should look at the logs in:

`cat /var/log/message`

You will find an error message there:\
`mounted filesystem with ordered data mode opts[null]`

This means that maybe the system had a crash of some kind. So you should try to run `fsck`.

Or, remember this useful sentence:

**EXT3 and EXT4** have two main modes to handle journaling:

* **Ordered data mode** and **Data writeback mode**

The **Data writeback mode** is generally considered to be much faster and better than **Ordered data mode**.\
**To change the default:**

**sudo tune2fs -o journal\_data\_writeback /dev/sdX**

**Also--to force a fsck on boot:**

**sudo touch /forcefsck && sudo reboot**

However, another possible cause of this problem is that the file system already has a **systemd mount unit** created for it.

Therefore, when systemd starts up, it looks at the **`.mount`** unit file. If there are **entries in `fstab`** for the file system, and a mount unit is also created for them, then you cannot mount the same location twice at the same time.

The solution is to make systemd **reload** its configuration via daemons, using the command:

**`systemctl daemon-reload`**
