## Docker running three containter on a Linux system
![alt text](image/image.png)

Praise for the first edition

- All there is to know about Docker. Clear, complete, and precise.

- A compelling narrative for real-world Docker solutions. A must-read!

- An indispensable guide to understanding Docker and how it fits into your infrastruture.

- Will help you transition quickly to effective Docker use in complex real-world situations.

- a superlative introduction to, and reference for, the docker ecosystem.

# Contents
### 1. Welcome to Docker 
    #### 1.1 What is Docker?
    #### 1.2 What problems does Docker solve?
    #### 1.3 Why is Docker important?
    #### 1.4 Where and when to use Docker
    #### 1.5 Docker in the larger ecosystem
    #### 1.6 Getting help with the Docker command line

## PART 1: PROCESS ISOLATION AND ENVIRONMENT-INDEPENDENT COMPUTING
### 2. Running software in containers
### 3. Software installation simplified
### 4. Working with storage and volumes
### 5. Single-host networking
### 6 Limiting risk with resource controls

## PART 2: PACKAGING SOFTWARE FOR DISTRIBUTION
### 7. Packaging software in images
### 8. Building images automatically with Dockerfiles
### 9. Public and private software distribution
### 10. Image pipelines

## PART 3: 3HIGHER-LEVEL ABSTRACTIONS AND ORCHESTRATION

### 11. Services with Docker and Compose
### 12. First-class configuration abstractions
### 13. Orchestrating services on a cluster of Docker hosts with swarm

# Foreword - read done

# Preface
Docker and the container community have come a long way since we started partici-pating in 2013. And docker has changed in some unexpected ways since 2016, when jeff released the first edition of this book. Thankfully, most of the user-facing inter-faces and core concepts were maintained in a backward-compatible manner. The first two-thirds of the book needed updates only for additional feature or closed issues. As anticipated, part 3 of the previous edition needed a full rewrite. Since publication of the previous book, we've seen progress in orchestration, app connectivity, proprietary cloud container offerings, multicontainer app packaging, and functions-as-a-serice platforms. this edition focuses on the fundamental concepts and practices for using Docker containers and steers clear of rapidly changing technologies that comple-ment Docker.

The biggest change is the develop and adoption of several container orchstra-tors. The primary purpose of a container orchestrator is to run applications modeled as services across a cluster of hosts.
Kubernetes, the most famous of these orchestration, has seen significant adoption and gained support from every major technology vendor.
The clould native computing foundation was formed around that project, and if you ask them, a "cloud native" app is one designed for deployment on Kubernetes.
But it is important not to get too caught up in the marketing or the specific orchestration technology. This book does not cover Kubernetes for two reasons.
While Kubernetes is include with docker for desktop, it is massive and in constant flux. It could never be covered at any depth in a handful of chapters or even in a book with fewer than 400 pages. A wealth of excellent resources are available online as well as wonderful published books on Kubernetes. We wanted to focus on the big idea-service orchestration in this book without getting too lost in the nuances.
Second, Docker ships with Swarm clustering and orchestration included. That system is more than  adequate for smaller clusters, or clusters in edge computing environments.
A huge number of organizations are happily using Swarm every day. Swarm is great for people getting started with orchestration and containers at the same time.
Most of the tooling and ideas varry over from containers to services with ease. Application developers will likely benefit the most from this approach. System administratiors or cluster operations personnel might be disappointed, or might find that Swarm meets their needs. But, we're not sure they'll ever find a long-form written resource that will satisfy their needs.

The next biggest change is that Docker runs everywhere today. Docker for Desktop is well intergrated for use Apple an Microsoft operating systems. It hides the underlying virtual machine from users. For the most part, this is a success; on macOS, the experience is nearly seamless. On Windows, things seen to go well at least for a few moments. Windows users will deal with an intimidating number of configuration variations from corporate firewall, aggressive antivirus configuration, shell preference, and serveral layers of indirection. That variation makes delivering written content for Windows impossible. Any attempt to do so would age out before the material went to production. For that reason, we've again limited the included syntax and system specific material to Linux and macOS. A reader just might find that all the examples actually run in their enviroment, but we can't promise that they will or reasonably help guide troubleshooting efforts.

# acknowledgments

# about this book

# about the authors

# about the cover illustration
