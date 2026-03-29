
## 📄 Lab 10 – Full Submission

You can view the complete lab write‑up here:

👉 [Lab10_DevSecOps_Ali_n10007505.pdf](Lab10_DevSecOps_Ali_n10007505.pdf)
or 
👉 [Lab10_DevSecOps_Ali_n10007505.docx](Lab10_DevSecOps_Ali_n10007505.dox)

Perfect — I’ll convert your entire lab submission into **clean, professional, GitHub‑ready Markdown**.  
You can paste this directly into your `README.md` and it will render beautifully.

---

# 📝 **Lab 10 — DevSecOps Pipeline**  
**Course:** CCGC‑5506 — Emerging Technologies in Cloud Computing  
**Student:** Ali — n10007505  

---

# ## **OWASP Top 10 — One‑Page Cheat Sheet (2021 Edition)**

### **1. Broken Access Control**
- **What:** Users can access data/actions they shouldn’t  
- **Danger:** Data leaks, privilege escalation  
- **Prevention:** Server‑side checks, RBAC, deny‑by‑default  

### **2. Cryptographic Failures**
- **What:** Weak or missing encryption  
- **Danger:** Data exposure, MITM attacks  
- **Prevention:** HTTPS, strong algorithms, hashed passwords  

### **3. Injection**
- **What:** Untrusted input executed as code  
- **Danger:** Database compromise, system takeover  
- **Prevention:** Parameterized queries, input validation  

### **4. Insecure Design**
- **What:** Missing security requirements  
- **Danger:** Systemic vulnerabilities  
- **Prevention:** Threat modeling, secure design patterns  

### **5. Security Misconfiguration**
- **What:** Unsafe defaults, exposed services  
- **Danger:** Unauthorized access  
- **Prevention:** Harden configs, disable unused features  

### **6. Vulnerable & Outdated Components**
- **What:** Libraries with known CVEs  
- **Danger:** Supply‑chain attacks  
- **Prevention:** Dependency scanning, updates  

### **7. Identification & Authentication Failures**
- **What:** Weak login/session management  
- **Danger:** Account takeover  
- **Prevention:** MFA, secure tokens, rate limiting  

### **8. Software & Data Integrity Failures**
- **What:** No integrity checks  
- **Danger:** Supply‑chain compromise  
- **Prevention:** Signed packages, secure CI/CD  

### **9. Logging & Monitoring Failures**
- **What:** Missing or ineffective logs  
- **Danger:** Undetected attacks  
- **Prevention:** Centralized logging, alerts  

### **10. SSRF**
- **What:** Server makes unauthorized requests  
- **Danger:** Access to internal networks  
- **Prevention:** Outbound allowlists, segmentation  

---

# ## **Part 1 — Code Review**

### **Question 1.1 — Identify OWASP Categories**

| Sample | OWASP Category | Why |
|--------|----------------|-----|
| **A** | Injection (A03:2021) | User input is concatenated into SQL → SQL injection risk |
| **B** | Broken Access Control (A01:2021) | `/admin` route has no auth checks |
| **C** | Cryptographic Failures (A02:2021) | Hardcoded credentials expose secrets |
| **D** | Vulnerable Components (A06:2021) | Outdated lodash version with known CVEs |

---

### **Question 1.2 — Secure SQL Query**

```js
app.get('/user', (req, res) => {
  const userId = req.query.id;
  const query = "SELECT * FROM users WHERE id = ?";
  db.execute(query, [userId], (err, results) => {
    res.json(results);
  });
});
```

---

### **Question 1.3 — Secure Credentials Using Environment Variables**

```js
require('dotenv').config();
const mysql = require('mysql');

const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD
});
```

---

# ## **Part 2 — Trivy Scans**

### **Question 2.1 — Filesystem Scan Results**
Trivy found **8 HIGH** vulnerabilities and **0 CRITICAL**.

Example HIGH vulnerability:  
**CVE‑2020‑8203 (lodash)** — Prototype pollution allowing attackers to modify application objects and potentially escalate privileges.

---

### **Container Image Comparison**
- **nginx:1.21** → many HIGH/MEDIUM vulnerabilities  
- **nginx:latest** → significantly fewer vulnerabilities  

**Conclusion:**  
Using newer base images reduces security risk because OS packages are patched.

---

# ## **Part 3 — Trivy in CI/CD**

### **Question 3.1 — Explain the Three Trivy Scans**

| Scan Type | What It Checks | Why It Matters |
|-----------|----------------|----------------|
| **Filesystem vulnerability scan** | Node.js dependencies | Prevents supply‑chain attacks |
| **Filesystem secret scan** | Hardcoded secrets | Prevents credential leaks |
| **Container image scan** | OS‑level packages in Docker image | Ensures the final artifact is secure |

Running all three ensures **full DevSecOps coverage**: code, secrets, and containers.

---

# ## **Part 4 — Secrets Detection**

### **Question 4.1 — Risk & Fix**

A `.env` file containing:

```
DATABASE_URL=postgresql://admin:Password123@prod-db.example.com:5432/myapp
```

is about to be committed.

### **Risk:**  
This exposes production database credentials. Attackers could connect, steal data, delete records, or take over the system.

### **Fix Steps:**
1. **Stop the exposure**  
   - Remove `.env` from the PR  
   - Add `.env` to `.gitignore`  
   - Rotate the leaked credentials  

2. **Move secrets to secure storage**  
   - Use GitHub Secrets / AWS Secrets Manager  
   - Load secrets via environment variables at runtime  

---

# ## **Part 5 — Reflection Questions**

### **5.1 — Shift Left**
Shift left means performing security earlier in development.  
Fixing vulnerabilities early is cheaper, faster, and avoids production outages.

---

### **5.2 — SAST vs DAST vs SCA**

| Type | Tool Example | When It Runs |
|------|--------------|--------------|
| **SAST** | CodeQL | During coding / build |
| **DAST** | OWASP ZAP | After deployment to staging |
| **SCA** | Trivy / Dependabot | During build & dependency updates |

---

### **5.3 — OWASP Top 10 Prevention**

**A01 Broken Access Control**  
- Enforce RBAC  
- Validate permissions server‑side  

**A03 Injection**  
- Use prepared statements  
- Validate and sanitize input  

---

### **5.4 — Zero Trust Model**
Zero Trust assumes **no implicit trust** — every request must be authenticated and authorized.  
Unlike perimeter security, it protects cloud workloads where users connect from anywhere.

---

### **5.5 — Shared Responsibility Model**

**Cloud Provider:**  
- Physical security  
- Hardware, networking, hypervisor  

**Customer:**  
- Application security  
- IAM, secrets, encryption  
- Network rules and data protection  

---