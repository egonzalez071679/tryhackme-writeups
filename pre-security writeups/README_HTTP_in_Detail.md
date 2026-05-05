# 🌐 TryHackMe Write-Up: HTTP in Detail

## 📌 Overview
The **HTTP in Detail** room explores how the **Hypertext Transfer Protocol (HTTP)** enables communication between clients (browsers) and web servers. HTTP is the foundation of web traffic and is heavily analyzed in cybersecurity for detecting attacks such as web exploitation, data exfiltration, and command-and-control (C2) activity.

---

## 🎯 Objectives
- Understand how HTTP works  
- Learn the structure of HTTP requests and responses  
- Identify common HTTP methods and status codes  
- Explore headers and their role in communication  
- Recognize HTTP-based security risks  

---

## 🌐 Key Concepts & Findings

### 1. What is HTTP?
HTTP is a stateless application-layer protocol used for transmitting web data.

**Key Points:**
- Operates over TCP (typically port 80)  
- Stateless (each request is independent)  
- HTTPS adds encryption using TLS  

---

### 2. HTTP Requests
An HTTP request is sent from the client to the server.

**Structure:**
- Request Line (Method, URL, Version)  
- Headers  
- Body (optional)  

**Common Methods:**
- GET → Retrieve data  
- POST → Send data  
- PUT → Update resource  
- DELETE → Remove resource  

**Example:**
```
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
```

---

### 3. HTTP Responses
The server replies with a response.

**Structure:**
- Status Line  
- Headers  
- Body  

**Example:**
```
HTTP/1.1 200 OK
Content-Type: text/html
```

---

### 4. HTTP Status Codes
- 200 OK → Success  
- 301/302 → Redirection  
- 400 Bad Request → Client error  
- 403 Forbidden → Access denied  
- 404 Not Found → Resource missing  
- 500 Internal Server Error → Server issue  

---

### 5. HTTP Headers
Common headers:
- User-Agent  
- Host  
- Cookie  
- Authorization  

---

### 6. Cookies & Sessions
- Maintain user state  
- Used for authentication  

**Risks:**
- Session hijacking  
- Cookie theft  

---

## 🛠️ Skills Gained
- Analyze HTTP traffic  
- Understand request/response structure  
- Identify suspicious activity  

---

## 🚨 Security Implications
- Detect malicious requests  
- Identify scanning activity  
- Investigate anomalies  

---

## 🧠 Key Takeaways
- HTTP is foundational  
- Major attack surface  
- Monitoring is critical  

---

## 📊 SOC Analyst Relevance
- Threat detection  
- Incident response  
- Log analysis  
- Network monitoring  

---

## 📁 Portfolio Value
- Web protocol knowledge  
- Security analysis skills  

---

## 🏁 Conclusion
Understanding HTTP is essential for analyzing web traffic and detecting cyber threats.
