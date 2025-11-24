# Monitoring the Dockerized Chat Application with Nagios Core

This document outlines the steps taken to configure **Nagios Core** to monitor the containerized chat application running via Docker Compose on the Windows host machine.

---

## 📋 Overview

**Nagios Core** is an industry-standard monitoring tool integrated to ensure the availability and basic health of the chat application's key components:
- **Frontend** (React Application)
- **Backend** (Node.js API)  
- **Database** (MongoDB)

---

## 🛠️ Implementation Steps

### 1. **Identify Target Environment**
- Determined that the chat application runs in Docker containers on Windows host machine
- Nagios monitoring configured from separate Linux VM

### 2. **Network Configuration**
- **VirtualBox Settings:**
  - Switched Nagios VM and Remote Host VM network adapters from `NAT` to `Host-Only Adapter`
  - Created private network (`192.168.56.x`) for VM ↔ Windows host communication
  - Added second network adapter (`Adapter 2`) set to `NAT` on Nagios VM for internet access

### 3. **Host Discovery**
- Identified Windows host IP address on Host-Only network: `192.168.56.1`
- Used `ipconfig` command to verify network configuration

### 4. **Nagios Host Definition**
```bash
# Created configuration file
/usr/local/nagios/etc/objects/chatapp-host.cfg

# Defined host
chatapp-server (IP: 192.168.56.1)
```

### 5. **Service Monitoring Configuration**
Configured monitoring for key application components:

| **Service** | **Check Type** | **Port** | **Purpose** |
|-------------|----------------|----------|-------------|
| Frontend HTTP | `check_http` | `5173` | Web server availability |
| Backend Port | `check_tcp` | `5001` | API service status |
| MongoDB Port | `check_tcp` | `27018` | Database connectivity |

### 6. **Nagios Configuration Update**
```bash
# Added to main config file
echo "cfg_file=/usr/local/nagios/etc/objects/chatapp-host.cfg" >> /usr/local/nagios/etc/nagios.cfg
```

### 7. **Host Check Troubleshooting**
- **Issue:** Default ping check failed (Windows firewall blocking ICMP)
- **Solution:** Implemented workaround using TCP port check
- **Workaround:** Modified host definition to use `check_command check_tcp!5173`

### 8. **Configuration Verification**
```bash
# Validate configuration
nagios -v /usr/local/nagios/etc/nagios.cfg

# Restart service
systemctl restart nagios
```

### 9. **Application Startup**
```powershell
# Start monitored application
docker-compose up -d
```

---

## ✅ Monitoring Results

### **Current Status:**
- ✅ **Host Status:** `chatapp-server` shows as **UP**
- ✅ **Frontend HTTP:** Service status **OK**
- ✅ **Backend Port:** Service status **OK**  
- ✅ **MongoDB Port:** Service status **OK**

### **Monitoring Capabilities:**
- Real-time service availability detection
- Automatic failure notifications
- Historical status tracking
- Performance metrics collection

---

## 🎯 Demo Presentation Guide

### **What to Explain:**

1. **Purpose & Value:**
   - Industry-standard monitoring for production-grade applications
   - Proactive issue detection and alerting
   - Ensures high availability and reliability

2. **Technical Setup:**
   - Nagios running in isolated Linux VM
   - Cross-platform monitoring (Linux → Windows containers)
   - Network segmentation using Host-Only adapter

3. **Monitoring Scope:**
   - **Host Check:** Frontend port responsiveness (workaround for ping blocking)
   - **Service Checks:** TCP connectivity and HTTP response validation

### **Live Demonstration Steps:**

#### **Step 1: Show Dashboard**
```
1. Open Nagios Web Interface: http://192.168.56.103/nagios
2. Navigate to "Hosts" → Show chatapp-server status
3. Navigate to "Services" → Display all monitored services
4. Point out OK status for all components
```

#### **Step 2: Failure Simulation (Optional)**
```powershell
# Stop backend container
docker-compose stop backend
```

**Expected Result:**
- Service status: `OK` → `PENDING` → `CRITICAL`
- Error message: "Connection refused"
- Demonstrates real-time monitoring capability

#### **Step 3: Recovery Demonstration**
```powershell
# Restart backend container  
docker-compose start backend
```

**Expected Result:**
- Service status: `CRITICAL` → `PENDING` → `OK`
- Shows automatic recovery detection

---

## 🔧 Configuration Files

### **Host Definition** (`chatapp-host.cfg`)
```cfg
define host {
    use                     linux-server
    host_name               chatapp-server
    alias                   Chat Application Server
    address                 192.168.56.1
    check_command           check_tcp!5173
    max_check_attempts      3
    check_period            24x7
    notification_interval   30
    notification_period     24x7
}

define service {
    use                     generic-service
    host_name               chatapp-server
    service_description     ChatApp Frontend HTTP
    check_command           check_http!-p 5173
}

define service {
    use                     generic-service
    host_name               chatapp-server
    service_description     ChatApp Backend Port
    check_command           check_tcp!5001
}

define service {
    use                     generic-service
    host_name               chatapp-server
    service_description     ChatApp MongoDB Port
    check_command           check_tcp!27018
}
```

---

## 📊 Key Benefits Achieved

1. **🔍 Real-time Monitoring:** Continuous health checks every few minutes
2. **🚨 Proactive Alerting:** Immediate notification of service failures
3. **📈 Availability Tracking:** Historical uptime and performance data
4. **🛡️ Production Readiness:** Industry-standard monitoring implementation
5. **🔧 Scalable Architecture:** Easy to add more services and hosts

---

## 🎯 Presentation Key Points

> **"This Nagios monitoring setup provides real-time visibility into our application's core components, enabling quick detection of outages or connectivity issues - essential for any production environment."**

### **Technical Highlights:**
- Cross-platform monitoring architecture
- Container-aware monitoring strategy  
- Network isolation and security considerations
- Automated failure detection and recovery tracking

### **Business Value:**
- Reduced downtime through proactive monitoring
- Improved user experience reliability
- Operational visibility and alerting
- Scalable monitoring foundation for growth

---

## 🔗 Quick Reference

| **Component** | **URL/Command** | **Purpose** |
|---------------|-----------------|-------------|
| Nagios Web UI | `http://192.168.56.103/nagios` | Dashboard access |
| Chat App | `http://localhost:5173` | Application frontend |
| Config Check | `nagios -v /usr/local/nagios/etc/nagios.cfg` | Validate setup |
| Service Restart | `systemctl restart nagios` | Apply changes |
| Container Control | `docker-compose stop/start [service]` | Demo failures |

---

*This monitoring implementation demonstrates enterprise-level DevOps practices and production-ready application architecture.*