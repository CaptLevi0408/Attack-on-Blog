+++
author = "CaptLevi0824"
title = "Shoppy Write-up - Easy"
date = 2023-08-12T09:02:42-07:00
description = "Shoppy"
draft = false
tags = [
    "HackTheBox",
    "Machines",
    "Retired"
]
+++

A remarkably comprehensive exposition on Shoppy from HackTheBox.

<!--more-->

# **A Write-Up on "Shoppy" from HackTheBox**
By: CaptLevi0824

![Alt Text](/images/Shoppy/Shoppy.png)

## **Description**
"Shoppy," a box found on HackTheBox (HTB), is thoughtfully categorized as an "easy" challenge, indicative of its manageable difficulty level. However, don't be deceived by its classification; while the challenge is accessible, it requires a versatile skill set encompassing various aspects of cybersecurity. Successfully seizing both the user and root flags demands a holistic approach, involving an array of techniques such as initial reconnaissance using nmap or preferred scanning tools, meticulous subdomain and directory enumeration through tools like gobuster and ffuf, adept utilization of SQL injection techniques, proficient handling of hash cracking tools like john the ripper and hashcat, a flair for privilege escalation strategies, and an adept command over fundamental Docker operations.
As I meticulously detail my triumphs and strategies within the intricate landscape of Shoppy, I remind myself that my work serves a dual purpose. It's a canvas on which I capture my own growth, preserving each step of my journey towards mastery. Simultaneously, it transforms into a guiding compass for those embarking on their own adventures in the captivating realm of ethical hacking. This write-up transcends its title; it's a vehicle of knowledge-sharing, nurturing a spirit of collaboration and empowerment within our close-knit community.
## **Reconnaissance**
The reconnaissance phase is of paramount importance when conducting penetration testing, as it forms the foundation for a successful assessment by providing invaluable knowledge and insights into the target system. By meticulously gathering information about the target, you can make informed decisions on the most effective approach to take, ensuring a more targeted and efficient penetration testing process.
For instance, initiating the reconnaissance process with an NMAP scan is a common and effective practice. NMAP's port scanning capabilities allow you to identify open ports and services on the target system. This initial scan could reveal ports 80 and 443 as open, suggesting the presence of a web server or a web page hosted on the box. This insight enables you to hypothesize potential attack vectors and vulnerabilities that might be associated with the identified services.
Beyond NMAP, employing other scanning tools such as Zenmap, Masscan, and Hping3 can provide additional perspectives and insights into the target's security posture. Zenmap, for instance, offers a graphical interface that can help visualize the network topology and relationships between systems. Masscan, on the other hand, excels in high-speed port scanning, potentially uncovering less common or hidden services. Hping3 enables you to perform more specialized scans, including advanced network testing and traceroute analysis.
Transitioning from the context of Hack The Box (HTB) to the real world, the benefits of reconnaissance remain highly relevant and extend beyond just penetration testing:

- **Vulnerability Management**: In a real-world scenario, organizations can utilize reconnaissance to proactively identify vulnerabilities in their own networks and systems. This aids in prioritizing and patching potential security gaps before malicious actors exploit them.
- **Threat Intelligence**: Gathering information about potential threats and attack vectors can aid in developing effective strategies to defend against them. This could involve studying the tactics, techniques, and procedures (TTPs) of known threat actors.
- **Incident Response**: During security incidents, having thorough knowledge of the compromised environment is crucial for effective incident response. Rapidly identifying the scope of an attack and the affected assets allows for a more precise and timely containment and recovery process.
- **Business Continuity**: Understanding the critical assets and dependencies within a network helps organizations build resilient systems. This knowledge contributes to maintaining business continuity even in the face of cyber incidents.
- **Compliance and Auditing**: Many industries are subject to regulatory compliance standards that mandate regular security assessments. Reconnaissance assists in preparing for audits by identifying potential vulnerabilities that could lead to non-compliance.

In conclusion, the reconnaissance phase serves as the foundation for successful penetration testing, enabling testers to strategize and prioritize their efforts effectively. Moreover, these principles extend beyond simulated environments like HTB, proving invaluable in real-world security assessments and proactive defense strategies.

### **Nmap**

![Alt Text](/images/Shoppy/nmap_initial.png)

Embarking on the initial steps is always invigorating, and I often kickstart the process with a comprehensive TCP port scan. This approach yields valuable insights into the status of open and closed ports. As showcased in the earlier-provided screenshot, I observed the existence of three accessible ports. My next move involves a deeper dive into these ports through enumeration, aimed at extracting further information from them. For context, I'd like to present a breakdown of the nmap command that I skillfully employed:

- **-p** flag is employed to specify the ports I want nmap to scan. The clever addition of a trailing hyphen after -p signals to nmap the intention to scan across all 65,535 ports.
- **-n** flag instructs nmap to bypass DNS resolution. While this might not be strictly necessary when scanning IP addresses instead of domains like "example.com," I habitually include it for consistency.
- **--disable-arp-ping** flag is a crucial inclusion, signaling nmap to deactivate ARP (Address Resolution Protocol) or IPv6 Neighbor Discovery.
- **--min-rate** flag empowers me to dictate the pace of packet transmission to nmap. In this scenario, nmap is configured to unleash a rapid flow of up to 10,000 packets per second, allowing for efficient scanning.
- **-oN** flag governs the output format of the scan. I've aptly labeled this particular scan as "nmap_initial."

However, it's important to emphasize the value of conducting diverse scans with each new box initiated on HTB. This practice provides an opportunity to expand your knowledge and familiarity with various scanning techniques. Different boxes can present unique challenges and configurations, so experimenting with alternate scan types offers a chance to gain insights into how different tools and methodologies interact with the target system. By incorporating varied scans, such as UDP scans, stealth scans, or version detection scans, you enhance your proficiency and adaptability in uncovering potential vulnerabilities and understanding the target's network layout. In the ever-evolving landscape of ethical hacking, versatility is a key asset, and the practice of employing different scans enriches your toolkit and skill set for tackling a wide range of scenarios effectively.

![Alt Text](/images/Shoppy/nmap_version.png)

To delve deeper into the open ports discovered during the initial scan, I chose to perform a version scan, aiming to precisely ascertain the services active on the system. As evidenced in the previously provided visual depiction, port 22 is serving OpenSSH version 8.4p1, port 80 is an HTTP port hosting nginx version 1.23.1, and port 9093 appears to be associated with a service labeled "copycat."

At this phase of the procedure, my standard approach involves halting the Nmap scans temporarily and transitioning to a more detailed enumeration of the identified ports. The ensuing steps encompass endeavors like establishing an SSH connection to the machine and delving into the content accessible via the URL http://10.129.227.233:80.

Below, I present an in-depth dissection of the Nmap command that I utilized for this specific scan:

- **-sV**: This flag directs nmap to initiate probes on open ports to gather information about services and their respective versions.
- **-p**: Employing this flag, I specified the ports I wished to scan, focusing on ports 22, 80, and 9093 in this instance.
- **--min-rate**: This flag empowers nmap to dispatch packets at a pace determined by the user. In this scenario, nmap was set to transmit up to 5,000 packets per second.
- **-oN**: By employing this flag, I instructed nmap to generate the scan output in a standard format. I aptly named this scan "nmap_version."

To further enhance the comprehensiveness of the scan and capture even more nuanced information, you might consider the inclusion of additional flags:

- **-A**: Enables aggressive mode, encompassing OS detection, version detection, script scanning, and traceroute.
- **--script**: This allows you to specify scripts to run against the target, potentially uncovering specific vulnerabilities or information.
- **-O**: Requests nmap to attempt OS detection, aiding in the identification of the target system's operating system.

By judiciously incorporating these flags and adjusting the command based on the specific requirements of each target, you can expand your reconnaissance capabilities, gaining a more holistic understanding of the system's architecture and potential security weaknesses.

### **SSH Banner Grabbing on Port 22**

In the context of ethical hacking and penetration testing, banner grabbing is used as a technique to gather information about the target system. This information assists in building a comprehensive understanding of the target's technology stack, which is essential for identifying potential vulnerabilities, weaknesses, and points of entry.
Banner grabbing is typically employed during the reconnaissance phase of an attack, which involves collecting as much information as possible about the target without directly interacting with it. This phase sets the groundwork for the subsequent steps in an attack, helping the attacker make informed decisions about the most suitable techniques to use.
By extracting banners, an attacker can identify:

- **Software Versions**: Information about the version numbers of software applications, services, and even operating systems. This aids in identifying known vulnerabilities associated with specific versions.
- **Service Details**: Configurations and settings of services, protocols, and applications, which can offer insights into potential security flaws or misconfigurations.
- **Operating System**: Sometimes, banners can provide clues about the underlying operating system, which assists attackers in tailoring their strategies based on OS-specific vulnerabilities.
- **Custom Information**: Banners might contain custom messages set by administrators or developers, offering hints about the target organization's structure or internal practices.

![Alt Text](/images/Shoppy/ssh_initial.png)

In this phase, I initiate a connection to SSH port 22 with the intention of retrieving a banner. This banner serves as a window, allowing me to gain insights into the system's characteristics and potential vulnerabilities, shaping my approach for deeper exploration. As evident, I didn't receive a welcoming or friendly banner message.

### **Exploring Port 80 on the Host: Enumeration and Insights**

![Alt Text](/images/Shoppy/http_error.png)

When attempting to access the webpage, I faced an error. To resolve this issue, I needed to include "shoppy.htb" in the "/etc/hosts" file on the system. This addition would enable the system to correctly associate the domain with its corresponding IP address. To achieve this, I used the command **"echo shoppy.htb {ip} | sudo tee -a /etc/hosts."** The command echoed the domain and IP address into tee, which then appended the entry to the "/etc/hosts" file with administrative privileges. This adjustment ensured smooth access to the webpage by allowing the system to resolve the domain accurately.

Command Breakdown:

- **echo**: Outputs the specified text.
- **shoppy.htb**: The domain entry to be added.
- **{ip}**: Placeholder for the actual IP address. Do not require the brackets.
- **|**: Redirects output to another command.
- **sudo**: Executes the following command with administrative privileges.
- **tee**: Reads input and writes it to both standard output and files.
- **-a**: Appends output to the file.
- **/etc/hosts**: The target file where hostname-to-IP mappings are stored.

![Alt Text](/images/Shoppy/http_hosts.png)

Now that I'm on the webpage, I'm all set to kickstart the website enumeration process!

While closely examining the webpage, a striking message captures my immediate attention: "Shoppy beta coming soon! Stay tuned for the beta access!" This message hints at the possibility of undiscovered treasures lying beneath the surface—subdomains or concealed directories awaiting exploration.

To enrich my exploration, I'll employ various techniques. Firstly, I'll dive into the webpage's source code, inspecting it for any hidden comments, links, or JavaScript code that might reveal valuable information. For instance, using the browser's "View Page Source" option or inspecting elements can unveil insights that might not be visible on the rendered page.

Furthermore, I'll leverage browser developer tools to scrutinize the elements closely. By right-clicking on specific elements and selecting "Inspect," I can unveil underlying HTML structures and even modify content temporarily to see if there's hidden content or links.

Incorporating these methods will help me establish a comprehensive understanding of the webpage's architecture, identify potential vulnerabilities, and possibly unveil additional avenues for exploration.

![Alt Text](/images/Shoppy/gobuster_sub.png)

The provided image confirms our successful discovery of a subdomain. Notably, our success was facilitated by employing an alternative wordlist, specifically the "bitquark-subdomains-top100000.txt" list from "/usr/share/seclists/Discovery/DNS/". This choice proved instrumental in uncovering the subdomain. Furthermore, we enhanced this process by incorporating the term "mattermost" into the aforementioned wordlist, which was previously illustrated.

A breakdown of the command executed during this operation is as follows:

- The **"vhost"** mode in Gobuster is particularly useful for performing virtual host-based directory and file brute-forcing. It accomplishes this by appending the target domain to each request, effectively simulating access to various subdomains on the same target.
- The **"-u"** flag designates the target URL for Gobuster's scanning activity. In this instance, the target is "http://shoppy.htb," representing the domain or IP address of our target website.
- The **"-w"** flag is pivotal in specifying the wordlist file to be utilized for the brute-force attack. In our command, we referenced the "subdomains-top1million-5000.txt" wordlist, located at "/usr/share/seclists/Discovery/DNS/". This wordlist contains a comprehensive compilation of potential directory and file names that Gobuster will systematically attempt to access on the target site.
- The **"--append-domain"** flag is unique to Gobuster's "vhost" mode and serves the crucial role of appending the target domain name to each request. This step is indispensable for the virtual host-based enumeration technique, enabling Gobuster to effectively simulate interactions with distinct subdomains under the same target domain.

By incorporating these elements, we efficiently executed the subdomain discovery process.

![Alt Text](/images/Shoppy/mattermost2.png)

After adding the entry "mattermost.shoppy.htb" to our "/etc/hosts" file, I successfully accessed the "mattermost" subdomain, which led me to a login screen. As I currently do not possess the required credentials, it is a prudent approach to consider alternative enumeration strategies. One such approach involves delving into directory exploration and harnessing the capabilities of Gobuster to assist in this endeavor. This multi-pronged strategy aims to broaden our scope of investigation and potentially uncover valuable paths or information that might not be immediately apparent.

![Alt Text](/images/Shoppy/gobuster_dir.png)

The visual information at hand clearly demonstrates Gobuster's successful identification of several directories, some of which exhibit promising attributes, deserving of more comprehensive exploration. A comprehensive breakdown of the executed command for this investigative process is presented below:

- The **"dir"** flag is judiciously utilized to denote Gobuster's operational mode as "directory," signifying its intent to undertake directory-based brute-forcing.
- The **"-u"** flag serves as a directive, pinpointing the focal point of the enumeration. In this scenario, the target URL is precisely specified as "http://shoppy.htb," constituting the website's domain or IP address under scrutiny.
- The **"-w"** flag comes into play, designating the chosen wordlist file to be employed during the brute-force endeavor. In particular, the "common.txt" wordlist is summoned from the location "/usr/share/wordlists/dirb/." This carefully curated wordlist contains an array of potential directory and file appellations. Gobuster employs this comprehensive list to systematically attempt to access these resources on the target website, unveiling pathways that may otherwise remain obscured.

By orchestrating this nuanced sequence of directives, we effectively deploy Gobuster's capabilities to unearth a variety of directories, with select options displaying conspicuous potential. This methodical approach extends our reach into the target domain, revealing avenues of interest that merit further exploration.

![Alt Text](/images/Shoppy/shoppy_login.png)

The findings derived from the Gobuster scan unmistakably indicate that accessing the "/admin" directory triggers a subsequent redirection to the "/login" page. Upon navigating to this "/login" directory, we are promptly met with a login interface, which presents an intriguing opening for scrutinizing potential vulnerabilities, particularly those pertaining to SQL injection. This circumstance piques our curiosity and prompts us to consider conducting a systematic evaluation to ascertain the presence of any exploitable SQL injection weaknesses within the login mechanism.

![Alt Text](/images/Shoppy/shoppy_login_admin.png)

As depicted in the provided image, my current approach involves attempting to log in by utilizing the payload "admin' || '1==1". The rationale behind employing this specific payload lies in its potential to exploit SQL injection (SQLi) vulnerabilities present within the target system. Successful execution of this payload would effectively indicate the susceptibility of the target to SQL injection, subsequently providing unauthorized access to the login mechanism. Let's delve into a comprehensive breakdown of the payload's components and the underlying mechanics of this attack:

- **"admin"**: This portion of the payload is a standard username that one would typically employ to log into the website's authentication system.
- **"||" (Double Pipe)**: The double pipe is a recognized SQL syntax element used to denote a logical OR condition within SQL queries.
- **"1==1"**: This statement signifies a simple condition within SQL that evaluates to true. The expression "1==1" is a logical truism in SQL, ensuring that the condition always holds true.

In SQL injection attacks, the goal is to manipulate the input fields in such a way that the underlying SQL queries are altered to execute unintended actions. By appending the payload "admin' || '1==1" to the username field during the login attempt, we are effectively crafting an injected SQL query like this:

` SELECT * FROM users WHERE username = 'admin' || '1==1' AND password = '...' `

In this query, the logical OR condition "1==1" ensures that the overall condition evaluates to true, bypassing the password check entirely. As a result, the authentication process is effectively circumvented, granting us access to the login interface without requiring a valid password.

This attack capitalizes on poor input validation and inadequate handling of user inputs within the SQL query, effectively manipulating the query's logic to achieve unauthorized access. It underscores the importance of robust input validation and secure coding practices to prevent such vulnerabilities from being exploited.

![Alt Text](/images/Shoppy/logged_in.png)

Triumph! During our thorough exploration of the page's elements, we've discerned that the sole viable option at our disposal is to delve into the functionality provided by the "Search for users" button. In light of this revelation, my next course of action involves injecting the same payload that proved successful during the login phase into the search field. By executing this step, I aim to meticulously observe the ensuing outcome and glean insights into the system's behavior and potential vulnerabilities. This methodical approach allows us to uncover any latent security weaknesses that could be exploited to our advantage.

![Alt Text](/images/Shoppy/hashes.png)

Following the search query input of "admin' || '1==1", a significant breakthrough ensued. The result of this search effort led to the successful retrieval of a page containing a pair of password hashes. Subsequently, a redirection directed me towards a page titled "/exports/export-search.json," where an additional pair of password hashes were exposed. In a strategic move to ensure the retention of these critical hashes, the decision was made to safeguard them by saving them within a text file.

With the hashes secured, the next logical step revolves around harnessing the capabilities of the "John" tool. By leveraging this potent tool, we can initiate a systematic cracking attempt aimed at deciphering the passwords behind the hashes. This methodical approach holds the potential to unveil the original passwords, thereby granting us access to valuable information and potentially furthering our progression through the system's defenses.

![Alt Text](/images/Shoppy/john_failed.png)

As illustrated in the accompanying image, my endeavor to unveil the password through the execution of the command "john --wordlist=/usr/share/wordlists/rockyou.txt hash" yielded no success. Nonetheless, upon scrutinizing the output provided by the "John" tool, an intriguing proposition emerged—a suggestion to explore the utilization of a "format" option.

In anticipation of a more positive outcome, I intend to offer an exhaustive breakdown of the command outlined above once the cracking process achieves its goal. This meticulous approach aligns with the intent of maximizing our comprehension of the operation and its outcomes, enabling us to draw valuable insights from the experience and refine our techniques for future endeavors.

![Alt Text](/images/Shoppy/HI_admin.png)

![Alt Text](/images/Shoppy/HI_josh.png)

After analyzing the information found on both images, I arrived at the decision to experiment with the “Raw-MD5” format option.

![Alt Text](/images/Shoppy/Cracked_hash.png)

Triumphantly, our persistence has borne fruit as we successfully managed to crack one of the previously enigmatic hashes, unveiling a password of significant value. In light of this achievement, allow me to provide an intricate dissection of the command that underpinned our accomplishment:

- **--wordlist=**: This indispensable component of the command serves as a vehicle for specifying the precise path and filename of a text file harboring a comprehensive assortment of words, aptly known as a "wordlist" or "dictionary." This carefully curated list functions as a repository of potential passwords that the "John" tool systematically employs to undertake the task of cracking the targeted password hash. In our strategic maneuver, I opted for the rockyou.txt wordlist—a renowned entity within cybersecurity circles, stemming from a notorious data breach in 2009.
- **hash**: The pivotal file that holds within it the previously enigmatic hashed passwords, enabling the "John" tool to deploy its arsenal of techniques to unravel their concealed contents.
- **--format=**: This option emerges as a linchpin in the command structure, steering our effort toward successful password cracking. Through this mechanism, we explicitly specify the format of the password hashes destined for decryption. The diverse array of systems and applications adopt varying methods to hash and store passwords. Hence, equipping "John" with the correct format is pivotal to deploying tailored cracking strategies. In our context, the "Raw-MD5" format was judiciously chosen, a decision informed by insights gleaned from "hash-identifier," a tool adept at discerning hash types.

By navigating this intricate command orchestration, we expertly harnessed the potential of "John" and the carefully selected tools at our disposal. This concerted effort culminated in our acquisition of a password— a key that opens doors to further exploration and deeper penetration of the system's defenses.

![Alt Text](/images/Shoppy/josh_mattermost.png)

I was successfully able to login to the “mattermost” subdomain with Josh’s credentials. Now to investigate what we can accomplish with these credentials.

![Alt Text](/images/Shoppy/jaeger_pw.png)

As evident from the image above, I discovered messages exchanged between Josh and another user, “Jaeger.” For some inexplicable reason, Josh decided to send Jaeger’s username and password through a message. Armed with Jaeger’s credentials, we now have an opportunity to attempt accessing SSH and potentially gain a foothold!

![Alt Text](/images/Shoppy/ssh_login.png)

Success! I have gained access! Now, it’s time to perform enumeration and explore the possibilities available to us while logged in as the user Jaeger.

Note: Now, the “user.txt” flag is accessible to us in Jaeger’s home directory.

![Alt Text](/images/Shoppy/messages.png)

Based on the contents of this message, it appears that Josh is informing Jaeger about creating a password manager and offering to test it if Jaeger is interested. Well, I’m certainly eager to give it a try! 

![Alt Text](/images/Shoppy/PW_manager.png)

Attempting to use Jaeger’s password to authenticate did not work out. Looks like the password-manager is asking for a master password, which we do not know.

![Alt Text](/images/Shoppy/http_server.png)

![Alt Text](/images/Shoppy/wget_ghidra.png)

Evident in the presented visuals, I initiated the process by establishing an HTTP server using Python. Subsequently, I executed the "wget" command to facilitate the seamless transfer of files from the remote source to my attack environment. With these essential preparations underway, I embarked on the subsequent phases of the operation.

Installing the sophisticated tool "Ghidra" marked a pivotal turning point. This powerful tool empowers reverse engineering endeavors, allowing for an in-depth dissection of the password manager binary's intricate workings. The primary objective here is to gain insight into the source code of the binary, unraveling its underlying logic, algorithms, and security mechanisms.

The step-by-step breakdown of the procedure is as follows:

- **Starting an HTTP Server**: The process commenced by deploying a Python-based HTTP server. This server setup serves as a conduit for sharing files from the attacker's machine to the targeted system.
- **Utilizing "wget" for File Retrieval**: By employing the "wget" command, the chosen files were seamlessly fetched from the remote HTTP server. This file transfer mechanism ensures the files are readily accessible and poised for further analysis.
- **Ghidra Installation and Reverse Engineering**: A significant progression unfolded with the installation of "Ghidra," a formidable tool designed to facilitate reverse engineering. This involves scrutinizing the binary's assembly code, data structures, and high-level language representations, providing invaluable insights into the binary's functionality.

The aim of this multifaceted endeavor is to unveil the inner workings of the password manager's binary code. By dissecting the source code, we unravel the blueprint that underpins its operation—illuminating pathways, vulnerabilities, and potential avenues for manipulation. The ultimate goal is to harness this comprehension for strategic exploitation, bolstering our capabilities in penetrating the system's defenses and gaining the upper hand in the security landscape.

![Image Alt Text](/images/Shoppy/ghidra_master.png)

Looking at the screenshot above, I opened Ghidra by typing in “ghidra”, and loaded up the password-manager to begin reverse engineering.

From analyzing the code, it becomes evident that the master password we provided is stored in the string variable local_48 on lines 15 to 18. Additionally, there is a comparison operation on local_48 at line 37, and lines 25 to 36 show the step-by-step construction of a string character by character, which resolves to "Sample." This implies that "Sample" is indeed the master password.

![Alt Text](/images/Shoppy/deploy_creds.png)

Using the master password “Sample” in the password manager application allows us to gain access, and we receive a set of credentials for the user “deploy.”

![Alt Text](/images/Shoppy/deploy_ssh.png)

In the current phase of exploration, it becomes evident that the deploy user account boasts the privilege to execute Docker commands. Building upon this revelation, I embark on the next step of my journey by invoking the "docker images" command. This command furnishes a comprehensive roster of Docker images resident within the system's framework.

Docker images function as the fundamental building blocks of Docker containers, encapsulating a self-contained, lightweight, and self-sufficient ensemble of software components. Each image is a self-sustained unit, meticulously housing all the requisite constituents vital for launching an application. This all-inclusive encapsulation encompasses not only the application's code but also its runtime environment, libraries, dependencies, and essential system tools.

This strategic maneuver grants us an invaluable insight into the system's software landscape. By perusing the list of available Docker images, we acquire a holistic overview of the tools, applications, and resources accessible within the Docker ecosystem. This newfound understanding paves the way for informed decisions and tactical choices, propelling us towards the successful navigation of the system's intricacies and the realization of our overarching objectives.

![Alt Text](/images/Shoppy/docker_images.png)

As I delved into the realm of Docker privilege escalation, my research led me to stumble upon a website housing a potentially valuable experimentation command. The original command caught my attention: "docker run --rm -it -v /:/host ubuntu bash." Recognizing its promise, a unique idea sparked in my mind, prompting me to refine and adapt the command to align with my objectives.

With this in mind, I decided to modify the command to "docker run -v /root:/mnt -it alpine." This revised command was carefully crafted to achieve a specific purpose. The goal was to launch an Alpine Linux container that would incorporate a mechanism to seamlessly mount the host system's "root" directory onto the designated "mnt" directory within the container itself.

Envisioning this configuration as a potential game-changer, I anticipated that it could provide me with an opportunity to directly engage with the host system's "root" directory from within the bounds of the Alpine container. This setup would potentially grant me access to and control over the files within the host's "root" directory, offering a valuable means to glean insights and potentially execute strategic actions.

This modification was a testament to my resourcefulness, showcasing an inventive approach to Docker privilege escalation. It presented a promising route to unravel the intricacies of this domain and explore avenues for meaningful interaction and manipulation.

![Alt Text](/images/Shoppy/root.png)

Success! We have gained root access inside the Docker image. If you recall where the previous command mounted the “/root” directory, you can use the cat command to read the contents of the "root.txt" file.

![Alt Text](/images/Shoppy/roottxt.png)

And there you have it!

I'm confident that this walkthrough has been an engaging and informative journey for those who have followed along. My intention was to offer thorough explanations, catering to individuals who might be new to this realm. Throughout this guide, I aimed to foster a deeper comprehension by leaving no stone unturned. HackTheBox stands out as an exceptional learning platform, and I strongly advocate for a shift in mindset—prioritizing the acquisition of skills to conquer challenges rather than merely chasing after flags.

By dedicating yourself to mastering these skills, you embark on a path that leads to not only successful flag acquisition but also a genuine understanding of the intricacies at play. This philosophy embraces the joy of learning and the thrill of ethical hacking. So, here's to continuous learning, joyful exploration, and the excitement of ethical hacking! Happy learning and may your hacking adventures be filled with success and knowledge!