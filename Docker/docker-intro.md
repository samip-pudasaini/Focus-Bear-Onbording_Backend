# Docker

Docker is an open platform for developing, shipping, and running applications.
Docker enables you to separate your applications from your infrastructure so you
can deliver software quickly. With Docker, you can manage your infrastructure in
the same ways you manage your applications. By taking advantage of Docker's
methodologies for shipping, testing, and deploying code, you can significantly
reduce the delay between writing code and running it in production.

## How Docker differs from Virtual Machine?

Virtual machine works by installing hypervisor instead of having multiple
physical server. So one server resource can be divided into multiple server with
each having independent Operating system.

**Hardware → Hypervisor → VM → Guest OS → Application**

As for docker it isolates application at the operating system level instead of
the server.

**Hardware → Host OS + Linux Kernel → Docker Engine → Container → Application**

This is lightweight because the linux Kernel is already in the architecture.

## Why is containerization useful for a backend like Focus Bear’s?

Containerization isolates a backend application and its environment into a
single package. this package runs identically on any computer. Since container
is self containded, so each container has everything it needs to function with
no reliance on any pre-installed dependencies on the host machine. Containers
can work anywhere, the container that runs on your development machine will work
the same way in a data center or anywhere in the cloud.

## How do containers help with dependency management?

Containers help with dependency management by packaging an application together
with all of its required dependencies, such as libraries, frameworks, runtime
versions, adn configuration.

For example, instead of installing Node.js, specific npm packages, and other
dependencies directly on the developer's computer, you can define them in a
Dockerfile and build a container containing everything the application needs.

## What are the potential downsides of using Docker?

Security risks from shared host kernel, a steep learning curve for networking
and data storage, complex management for large container. Scaling, and data loss
risks if storage volume are not configured properly.

**Common Downsides**

- Containers share the host operating system kernel, meaning a kernel exploit
  can compromise the entire host.
- Giving access to the Docker socket grants full root privileges to the host
  system.
- Untrusted community images can contain hidden malware or vulnerabilities.
- Users must learn specialized concepts like container networking, layers, and
  volume mounting.
- Troubleshooting issues inside a container can be harder than debugging a
  standard operating system environment.
- Managing multiple containers requires extra tools like Docker Compose or
  Kubernetes.
- Containers are temporary by design; unmapped data is deleted when a container
  stops.
