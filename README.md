# Secure Remote Access for SMEs

<img width="941" height="437" alt="image" src="https://github.com/user-attachments/assets/7069cfdd-4bd1-4d7f-84d8-cee406670a64" />

### Background to the Study
The onset of the COVID-19 pandemic brought about a fundamental shift in organizational business processes worldwide. This global change meant that a significant number of firms had to move to fully remote or hybrid work structures. The uninterrupted flow of business processes had to be maintained at all costs, so in remote work and hybrid work models had to be executed along with social distancing protocols and government regulations in place. Knowledge workers, a group that comprises those permeated with information and technology-stimulated work, surpassed the milestone of 70% remote work employment by Mid 2023 (Capita Works, 2024).

Such a tremendous transformation illustrates how a large section of the global workforce is now accustomed to the flexi-place of work. In particular, the move is profoundly appealing due to enhanced work life integration, higher levels of freedom, and being able to access talent beyond geographical frontiers. This is bound to happen, but the rate at which it will happen is what varies according to the pace at which Terminal work flexibility continues to gain limelight around the world, Lee & Park (2023); Smith & Doe (2022).

The hybrid paradigm enhances employee satisfaction and productivity and improves work life balance and commuting times. In addition, companies can widen their recruitment horizons and leverage a spatially distributed talent pool. On the other hand, the move to remote work poses serious cybersecurity issues. The ability to connect remotely is often via unsecure networks like home Wi-Fi, cellular data, and public Wi-Fi hotspots. Corporate systems have become far more vulnerable to major breaches. Cybersecurity threats like man in the middle (MITM) attacks, credential theft, endpoint compromises, and the influx of malware become serious issues. These threats undermine the fundamental principles of cybersecurity: confidentiality, integrity, and availability (CIA) (Mueller, n.d.; National Institute of Standards and Technology [NIST], n.d.; SentinelOne, 2025). In the absence of reliable protective systems, companies stand to face data leaks, operational downtime, monetary damage, and damage to reputation (Johnson & Wang, 2023).

This project aims to build a secure remote access infrastructure that uses industry vetted technologies, follows industry benchmarks, and incorporates continuous verification processes. The objective is to build a highly scalable, highly resilient, and easy to use framework that serves the intricate security requirements of hybrid employees.

As an illustration, consider a mid-sized, multinational company that, prior to 2020, depended heavily on perimeter-centric security models, employing firewalls and a limited capacity VPN. With fires, VPN capabilities, and remote work, these legacy systems were overstrained and suffered from a number of neglected password reuse, misconfiguration, and a horde of cybersecurity issues, from reuse and misconfigured VPNs (Kumar & Sharma, 2023; Al Ameen & Talukder, 2023). This project aims to create a controlled virtual environment to simulate and subsequently design, test, and iterate on remote access systems that address contemporary cyber security challenges.

---

### Tasks
1. Research cybersecurity threats for companies with remote workers, such as fake Wi Fi networks, weak passwords, and malware-infected devices.
2. Review books, articles, and technical papers on secure remote access technologies and their challenges.
3. Test three remote access tools: OpenVPN, WireGuard, and Zero Trust Network Access (ZTNA) for speed, ease of installation, and security.
4. Set up a virtual lab on VirtualBox with servers, firewalls, and different operating systems to simulate a corporate network.
5. Build a secure remote access system using the best tools, including VPN servers and multi-factor authentication.
6. Conduct penetration testing with Nmap and Nessus to find vulnerabilities in the system.
7. Measure performance with 10, 50, and 100 users, tracking speed, memory usage, and CPU usage.
8. Gather feedback from 5 to 10 volunteers doing common remote access tasks and document their suggestions.
9. Compile all research, development steps, test results, and user feedback into a detailed project report.

### Definitions
Definitions
For clarity and consistency, the following key terms are defined as they are used in this study:
* Small and Medium Sized Enterprises (SMEs): Businesses with fewer than 250 employees. They are often constrained by limited resources, which affects their ability to deploy advanced cybersecurity infrastructure and maintain dedicated security teams.
* Hybrid Work Environment: A flexible model in which employees alternate between remote and onsite work. While it improves organizational adaptability, it introduces new risks, especially in securing digital communication channels and maintaining consistent access policies.
* Remote Access Framework: A structured combination of tools, policies, and protocols that enable employees to securely connect to organizational resources from outside the physical office. This framework is central to supporting hybrid work models.
* Virtual Private Network (VPN): A technology that establishes encrypted communication over the internet, ensuring the confidentiality and integrity of transmitted data. In this study, WireGuard, an open source VPN, is implemented for secure connectivity.
* Firewall: A network security system designed to monitor, filter, and control incoming and outgoing traffic based on predefined rules. pfSense, an open source firewall, is utilized in this study to provide robust protection for SME networks.
* Multi Factor Authentication (MFA): A layered security approach that requires users to present two or more independent credentials before gaining access to systems. This research highlights open source tools such as Authelia and privacy IDEA.
* Data Governance: The comprehensive management of data availability, usability, security, and integrity within an enterprise. Strong governance becomes increasingly critical in hybrid environments where data flows across multiple access points.
* Bring Your Own Device (BYOD): A workplace policy permitting employees to use personal devices for organizational tasks. While cost effective, BYOD heightens exposure to security threats and requires strict control measures.
* Cybersecurity Awareness: The level of understanding employees and stakeholders possess regarding potential threats and safe online practices. This study emphasizes the role of awareness programs in mitigating human-related risks in SMEs.
* Regulatory Compliance: Adherence to national and international information security and data protection standards such as the GDPR, NIST Cybersecurity Framework, or Nigeria’s NDPA. Compliance ensures both legal protection and organizational credibility.
* pfSense: An open source firewall and router solution designed to safeguard organizational networks. It is highlighted in this research for its affordability and applicability to SMEs.
* WireGuard: An open source VPN protocol known for simplicity, speed, and modern encryption standards, used in this study to secure remote connections.
* Authelia: An open source authentication and authorization server that supports Single Sign On (SSO) and MFA, enhancing access control for SMEs.
* privacyIDEA: An open source system for managing multi factor authentication tokens, supporting delivery methods such as SMS, email, and authenticator apps.

yml
```
version: '3.3'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.13.4
    container_name: elasticsearch
    environment:
        discovery.type=single node
        ES_JAVA_OPTS= Xms1g  Xmx1g
        xpack.security.enabled=false
    ulimits:
      memlock:
        soft:  1
        hard:  1
    volumes:
        es_data:/usr/share/elasticsearch/data
    ports:
        "9200:9200"

  logstash:
    image: docker.elastic.co/logstash/logstash:8.13.4
    container_name: logstash
    volumes:
        ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro
    ports:
        "514:514/udp"     # Syslog UDP from pfSense
        "5044:5044"       # Beats input (optional for future)
    depends_on:
        elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:8.13.4
    container_name: kibana
    environment:
        ELASTICSEARCH_HOSTS=http://elasticsearch:9200
        SERVER_HOST=0.0.0.0   # Important for access via IP
    ports:
        "5601:5601"
    depends_on:
        elasticsearch

volumes:
  es_data:
    driver: local
```

### ELK Deployment Results
<img width="940" height="392" alt="image" src="https://github.com/user-attachments/assets/5a601539-d53f-4bcc-92d3-99fb0bc1ecdd" />

**Key Observations**
1.	The integration of pfSense, WireGuard, Nginx, and ELK Stack worked effectively for secure remote access, traffic filtering, and log monitoring.
2.	System performance remained stable during simulated multiple user connections.
3.	Authelia’s MFA feature could not be fully tested because the setup used localhost instead of a domain name.
4.	Using a proper domain and SSL certificates in future tests may enable full MFA functionality.
5.	The framework is suitable for SME deployment, requiring minimal resources.

**Summary**
Overall, the framework performed effectively, with all components functioning as expected except for Authelia, which encountered a domain related limitation during testing. The results validate the framework’s suitability for providing secure remote access in SME environments, with potential improvements in authentication deployment for production environments.


