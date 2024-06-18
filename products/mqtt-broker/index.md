---
layout: mqtt-broker
title: TBMQ
description: TBMQ is an industry-ready MQTT broker that facilitates MQTT client connectivity, message publishing, and distribution among subscribers.
---

<section id="top-features">
    <main>
        <div id="cards">
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/scalability.svg" alt="Scalability">
                    <div>
                        <h4 class="title">Scalability</h4>
                        <p>Scale horizontally to manage more than <a href="/docs/mqtt-broker/reference/100m-connections-performance-test/">100M</a> MQTT connections on a single cluster</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/performance.svg" alt="Performance">
                    <div>
                        <h4 class="title">Performance</h4>
                        <p>Process <a href="/docs/mqtt-broker/reference/3m-throughput-single-node-performance-test/">millions</a> of messages per second with 1 TBMQ server and single-digit latency</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/durability.svg" alt="Durability">
                    <div>
                        <h4 class="title">Durability</h4>
                        <p>Guarantee the persistence and replication of your data to ensure it's never lost</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/security.svg" alt="Fault tolerance">
                    <div>
                        <h4 class="title">Fault tolerance</h4>
                        <p>Prevent single point of failure with masterless nodes in the cluster</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/cloud-native.svg" alt="Cloud-Native and K8s-Compliant">
                    <div>
                        <h4 class="title">Cloud-Native and K8s-Compliant</h4>
                        <p>Deploy in cloud or on-premise using K8s scripts with ease</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/mqtt-broker/product/mqtt-version-compatibility.svg" alt="MQTT version compatibility">
                    <div>
                        <h4 class="title">MQTT version compatibility</h4>
                        <p>MQTT 3.x and 5.0 compatible for a seamless and secure connection experience</p>
                    </div>
                </div>
            </div>
        </div>
    </main>
</section>

<section id="scenarios">
    <main>
        <div id="background">
            <div class="main1"></div><div class="small1"></div><div class="small2"></div>
        </div>
        <div class="block first-scenario">
            <div class="feature-des">
                <h3 class="item-heading">MQTT broker for real-life IoT scenarios</h3>
            </div>
            <div class="feature-details">
                <p>Utilize diverse communication patterns effectively, ensuring comprehensive coverage of your use cases. TBMQ places particular emphasis on mastering fan-in and fan-out messaging.</p>
            </div>
        </div>
        <div class="block">
            <div class="description">
                <div class="fan-title">
                    <a href="/docs/mqtt-broker/reference/100m-connections-performance-test/" class="item-heading">Fan-in</a>
                </div>
                <div>
                    <span>Many-to-one communication pattern. Many devices generate a large volume of messages consumed by specific applications. They must be persistent clients with QoS levels set to 1 or 2, capable of retaining all the data even when they're temporarily offline due to restarts or upgrades.</span>
                </div>
            </div>            
            <div class="image-container">
                <img src="/images/mqtt-broker/product/fan-in.gif" alt="Fan in" loading="lazy">
            </div>
        </div>
        <div class="block">
            <div class="image-container fan-out-img">
                <img src="/images/mqtt-broker/product/fan-out.gif" alt="Fan out" loading="lazy">
            </div>            
            <div class="description">
                <div class="fan-title">
                    <a href="/docs/mqtt-broker/reference/3m-throughput-single-node-performance-test/" class="item-heading">Fan-out</a>
                </div>
                <div>
                    <span>This scenario facilitates one-to-many messaging. It involves numerous devices subscribing to specific updates or notifications that must be delivered. This leads to a few incoming requests that cause a high volume of outgoing data.</span>
                </div>
            </div>
        </div>
    </main>
</section>

<section id="installation-options">
    <main>
        <div id="background">
            <div class="main2"></div><div class="small3"></div><div class="small4"></div>
        </div>
        <div class="row">
            <div class="col-lg-6">
                <div class="block">
                    <h4>Diverse installation options</h4>
                    <p>Select the perfect fit for your infrastructure with our Docker-based or K8s-based deployments, crafted for seamless integration in both on-premise and cloud environments.</p>
                    <a href="/docs/mqtt-broker/install/installation-options/" class="button arrow-top-right">Install TBMQ</a>
                </div>
            </div>
            <div class="col-lg-6 installation-options-img">
                <div class="options">
                    <img src="/images/mqtt-broker/product/docker.webp" alt="Docker">
                    <img src="/images/mqtt-broker/product/aws.webp" alt="Aws">
                    <img src="/images/mqtt-broker/product/azure.webp" alt="Azure">
                    <img src="/images/mqtt-broker/product/google-cloud.webp" alt="Google Cloud">
                </div>
            </div>
        </div>
    </main>
</section>

<section id="middle-features">
    <main>
        <div class="row">
            <div class="col-lg-6">
                <div class="block">
                    <h4>Administer client sessions</h4>
                    <p>Gain full visibility over your IoT ecosystem with the user-friendly session management dashboard, enabling you to monitor vital session attributes. Seamlessly administer your device subscriptions for efficient data communication.</p>
                </div>
            </div>
            <div class="col-lg-6 image-container">
                <div class="block">
                    <img src="/images/mqtt-broker/product/administer-client-session.webp" alt="Administer client sessions" loading="lazy">
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-lg-6">
                <div class="block">
                    <h4 class="title">MQTT client credentials management</h4>
                    <p>Strengthen your IoT security using the MQTT client credentials management system, crafted to handle both Basic and X.509 Certificate Chain authentication options effortlessly.</p>
                </div>
            </div>
            <div class="col-lg-6 image-container">
                <div class="block">
                    <img src="/images/mqtt-broker/product/credentials-management.webp" alt="MQTT client credentials management" loading="lazy">
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-lg-6">
                <div class="block">
                    <h4 class="title">WebSocket client connectivity</h4>
                    <p>Interact with your IoT devices in real-time through the WebSocket client, offering a streamlined and responsive interface for device messaging and monitoring.</p>
                </div>
            </div>
            <div class="col-lg-6 image-container">
                <div class="block">
                    <img src="/images/mqtt-broker/product/websocket-client-connectivity.webp" alt="WebSocket client connectivity" loading="lazy">
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-lg-6">
                <div class="block">
                    <h4 class="title">Monitor key metrics</h4>
                    <p>Keep a pulse on your IoT environment by tracking key metrics in real-time, ensuring your network's health and performance are always at their peak.</p>
                </div>
            </div>
            <div class="col-lg-6 image-container">
                <div class="block">
                    <img src="/images/mqtt-broker/product/monitor-key-metrics.webp" alt="Monitor key metrics" loading="lazy">
                </div>
            </div>
        </div>
    </main>
</section>

<section id="bottom-features">
    <main>
        <div id="background">
            <div class="main3"></div><div class="small5"></div><div class="small6"></div>
        </div>
        <h3>TBMQ Features</h3>
        <div id="cards">
            <div class="col-lg-6">
                <div class="block">
                    <img src="/images/thingsboard-logo.svg" alt="MQTT client type">
                    <div>
                        <a href="/docs/mqtt-broker/user-guide/mqtt-client-type/"><h4 class="title">MQTT client type</h4></a>
                        <p>Supports two distinct client types to provide customized messaging solutions for diverse IoT scenarios. One type efficiently handles frequent data transmissions and effectively manages incoming moderate message volumes (fan-out). The other specializes in high-rate topic subscriptions, perfect for data analytics and processing (fan-in).</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block"><img src="/images/mqtt-broker/product/load-balancing.svg" alt="MQTT client Load Balancing">
                    <div>
                        <a href="/docs/mqtt-broker/user-guide/shared-subscriptions/"><h4 class="title">MQTT client Load Balancing</h4></a>
                        <p>Delivers an advanced solution for the even distribution of messages among multiple MQTT clients through a fault-tolerant, distributed message processing platform. Ensures no single client becomes overwhelmed, resulting in optimized resource utilization.</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block"><img style="margin: 0" src="/images/mqtt-broker/product/security.svg" alt="Security">
                    <div>
                        <a href="/docs/mqtt-broker/security/"><h4 class="title">Security</h4></a>
                        <p>Ensure secure communication with transport encryption for both MQTT and MQTT over WebSocket. Supports basic and X.509 Certificate Chain authentications.</p>
                    </div>
                </div>
            </div>
            <div class="col-lg-6">
                <div class="block"><img src="/images/mqtt-broker/product/ws.svg" alt="MQTT over WebSocket">
                    <div>
                        <a href="/docs/mqtt-broker/user-guide/mqtt-over-ws/"><h4 class="title">MQTT over WebSocket</h4></a>
                        <p>Experience seamless, secure, and real-time communication between web applications and MQTT networks. This powerful feature allows for easy integration of web-based interfaces with IoT devices.</p>
                    </div>
                </div>
            </div>
        </div>
    </main>
</section>

<section id="get-started">
    <main>
        <div id="background">
            <div class="main4"></div><div class="small7"></div><div class="small8"></div>
        </div>
        <div class="row">
            <div class="container">
                <div class="block">
                    <h4>Get started with TBMQ</h4>
                    <p>Get started with TBMQ today and unlock the full potential of your IoT solutions! With support for millions of concurrent connections, robust message handling, and reliable data persistence, TBMQ empowers you to build, scale, and secure your IoT applications like never before.</p>
                    <a href="/docs/mqtt-broker/getting-started/" class="button arrow-top-right">Get started</a>
                </div>
            </div>
        </div>
    </main>
</section>

<section id="latest-news">
    <main>
        <h3>Latest news</h3>
        <div id="cards">
            <div class="col-lg-6">
                <div class="block">
                    <div class="image-container">
                        <a href="/blog/tbmq-1-3-0-release-websocket-client-advanced-mqtt-5-features-and-more/">
                            <img src="/images/mqtt-broker/product/latest-news-1.webp" alt="TBMQ 1.3.0 release" loading="lazy">
                        </a>
                    </div>
                    <div class="content">
                        <div class="tags">
                            <a href="/blog/category/updates/">Updates</a>
                        </div>
                        <a class="title" href="/blog/tbmq-1-3-0-release-websocket-client-advanced-mqtt-5-features-and-more/">TBMQ 1.3.0 release: WebSocket client, advanced MQTT 5 features, and more</a>
                        <p>We’re delighted to introduce TBMQ version 1.3.0! This update improves MQTT over WebSocket functionality by introducing a new WebSocket client. It also broadens the scope of supported MQTT 5 features. Here’s an overview of the features and updates included in this release</p>
                    </div>
                </div>
            </div>
        </div>
    </main>
</section>
