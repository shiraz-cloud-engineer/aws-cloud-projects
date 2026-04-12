import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.HttpExchange;
import java.io.IOException;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class Welcome {
    public static void main(String[] args) throws IOException {
        HttpServer server = HttpServer.create(new InetSocketAddress(8080), 0);
        server.createContext("/", new HomeHandler());
        server.createContext("/about", new AboutHandler());
        server.createContext("/skills", new SkillsHandler());
        server.createContext("/projects", new ProjectsHandler());
        server.start();
        System.out.println("========================================");
        System.out.println("   Cloud Hub Server Started!");
        System.out.println("   Running on port 8080");
        System.out.println("   http://localhost:8080");
        System.out.println("========================================");
    }

    // ── Shared HTML parts ────────────────────────────────────────────────────

    static String getHead(String title) {
        return "<!DOCTYPE html><html lang='en'><head>" +
               "<meta charset='UTF-8'>" +
               "<meta name='viewport' content='width=device-width, initial-scale=1.0'>" +
               "<title>" + title + " | Cloud Hub</title>" +
               "<style>" +
               "* { margin:0; padding:0; box-sizing:border-box; }" +
               "body { font-family: 'Segoe UI', Arial, sans-serif; background:#0a0a1a; color:white; }" +

               // Navbar
               "nav { background:#111127; padding:15px 40px; display:flex; justify-content:space-between; align-items:center; border-bottom:2px solid #FF9900; position:sticky; top:0; z-index:100; }" +
               ".logo { font-size:22px; font-weight:bold; color:#FF9900; }" +
               ".logo span { color:white; }" +
               ".nav-links a { color:#ccc; text-decoration:none; margin-left:25px; font-size:15px; transition:color 0.3s; }" +
               ".nav-links a:hover { color:#FF9900; }" +

               // Hero
               ".hero { text-align:center; padding:80px 20px 60px; background:linear-gradient(135deg,#0a0a1a 0%,#111127 100%); }" +
               ".hero .badge { background:#FF9900; color:white; padding:8px 22px; border-radius:20px; font-size:13px; font-weight:bold; display:inline-block; margin-bottom:20px; }" +
               ".hero h1 { font-size:52px; margin-bottom:12px; line-height:1.2; }" +
               ".hero h1 span { color:#FF9900; }" +
               ".hero p { font-size:18px; color:#aaa; max-width:600px; margin:0 auto 30px; }" +

               // Stats bar
               ".stats { display:flex; justify-content:center; gap:40px; padding:30px; background:#111127; flex-wrap:wrap; }" +
               ".stat { text-align:center; }" +
               ".stat .number { font-size:36px; font-weight:bold; color:#FF9900; }" +
               ".stat .label { font-size:13px; color:#aaa; margin-top:4px; }" +

               // Cards
               ".section { padding:60px 40px; max-width:1100px; margin:0 auto; }" +
               ".section-title { font-size:28px; font-weight:bold; margin-bottom:8px; }" +
               ".section-title span { color:#FF9900; }" +
               ".section-sub { color:#aaa; font-size:15px; margin-bottom:35px; }" +
               ".cards { display:grid; grid-template-columns:repeat(auto-fit,minmax(280px,1fr)); gap:20px; }" +
               ".card { background:#111127; border:1px solid #222244; border-radius:12px; padding:25px; transition:transform 0.3s,border-color 0.3s; }" +
               ".card:hover { transform:translateY(-5px); border-color:#FF9900; }" +
               ".card-icon { font-size:36px; margin-bottom:15px; }" +
               ".card h3 { font-size:18px; margin-bottom:8px; color:#FF9900; }" +
               ".card p { font-size:14px; color:#aaa; line-height:1.6; }" +

               // Skills
               ".skills-grid { display:flex; flex-wrap:wrap; gap:12px; }" +
               ".skill-tag { background:#1a1a2e; border:1px solid #FF9900; color:#FF9900; padding:8px 18px; border-radius:8px; font-size:14px; font-weight:bold; }" +
               ".skill-tag.blue { border-color:#2196F3; color:#2196F3; }" +
               ".skill-tag.green { border-color:#2ECC71; color:#2ECC71; }" +
               ".skill-tag.purple { border-color:#9C27B0; color:#9C27B0; }" +
               ".skill-tag.red { border-color:#E74C3C; color:#E74C3C; }" +

               // Timeline
               ".timeline { border-left:3px solid #FF9900; padding-left:25px; margin-top:20px; }" +
               ".timeline-item { margin-bottom:30px; position:relative; }" +
               ".timeline-item::before { content:''; width:12px; height:12px; background:#FF9900; border-radius:50%; position:absolute; left:-31px; top:5px; }" +
               ".timeline-item h4 { color:#FF9900; font-size:16px; margin-bottom:5px; }" +
               ".timeline-item p { color:#aaa; font-size:14px; line-height:1.6; }" +
               ".timeline-item .date { color:#666; font-size:12px; margin-bottom:5px; }" +

               // Footer
               ".footer { background:#111127; border-top:2px solid #FF9900; padding:30px; text-align:center; color:#666; font-size:14px; margin-top:60px; }" +
               ".footer span { color:#FF9900; }" +

               // Live time
               ".live-time { background:#1a1a2e; border:1px solid #333; border-radius:8px; padding:8px 16px; display:inline-block; margin-top:15px; font-size:13px; color:#aaa; }" +
               ".live-time span { color:#FF9900; font-weight:bold; }" +

               // Buttons
               ".btn { display:inline-block; padding:12px 28px; border-radius:8px; font-size:15px; font-weight:bold; text-decoration:none; margin:8px; transition:all 0.3s; }" +
               ".btn-orange { background:#FF9900; color:white; }" +
               ".btn-outline { border:2px solid #FF9900; color:#FF9900; }" +
               ".btn:hover { opacity:0.85; transform:scale(1.03); }" +

               // Alert bar
               ".alert { background:linear-gradient(90deg,#FF9900,#ff6600); padding:12px; text-align:center; font-size:14px; font-weight:bold; }" +
               "</style></head><body>";
    }

    static String getNav() {
        return "<nav>" +
               "<div class='logo'>☁️ Cloud<span>Hub</span></div>" +
               "<div class='nav-links'>" +
               "<a href='/'>Home</a>" +
               "<a href='/about'>About</a>" +
               "<a href='/skills'>Skills</a>" +
               "<a href='/projects'>Projects</a>" +
               "</div></nav>";
    }

    static String getFooter() {
        String time = LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
        return "<div class='footer'>" +
               "Built with ☁️ by <span>Shiraz Siddiqui</span> | " +
               "Hosted on <span>AWS EC2</span> | " +
               "Java HTTP Server | " +
               "Server Time: <span>" + time + "</span>" +
               "</div></body></html>";
    }

    static void sendResponse(HttpExchange exchange, String html) throws IOException {
        byte[] bytes = html.getBytes("UTF-8");
        exchange.getResponseHeaders().set("Content-Type", "text/html; charset=UTF-8");
        exchange.sendResponseHeaders(200, bytes.length);
        OutputStream os = exchange.getResponseBody();
        os.write(bytes);
        os.close();
    }

    // ── HOME PAGE ────────────────────────────────────────────────────────────
    static class HomeHandler implements HttpHandler {
        public void handle(HttpExchange exchange) throws IOException {
            String html = getHead("Home") + getNav() +

            "<div class='alert'>🚀 Live on AWS EC2 — Java HTTP Server running on Port 8080</div>" +

            "<div class='hero'>" +
            "<div class='badge'>☁️ AWS Certified Cloud Engineer</div>" +
            "<h1>Hi, I'm <span>Shiraz</span> 👋</h1>" +
            "<p>Aspiring Cloud & DevOps Engineer building real-world AWS infrastructure projects</p>" +
            "<a href='/projects' class='btn btn-orange'>View Projects</a>" +
            "<a href='/about' class='btn btn-outline'>About Me</a>" +
            "<div class='live-time'>🟢 Server Online | Time: <span>" +
            LocalDateTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss")) +
            "</span></div>" +
            "</div>" +

            "<div class='stats'>" +
            "<div class='stat'><div class='number'>4+</div><div class='label'>AWS Projects</div></div>" +
            "<div class='stat'><div class='number'>1</div><div class='label'>AWS Certification</div></div>" +
            "<div class='stat'><div class='number'>3</div><div class='label'>Months Internship</div></div>" +
            "<div class='stat'><div class='number'>10+</div><div class='label'>AWS Services Used</div></div>" +
            "</div>" +

            "<div class='section'>" +
            "<div class='section-title'>What I <span>Do</span></div>" +
            "<div class='section-sub'>Building and managing cloud infrastructure on AWS</div>" +
            "<div class='cards'>" +
            "<div class='card'><div class='card-icon'>☁️</div><h3>Cloud Infrastructure</h3><p>Deploying and managing scalable AWS infrastructure using EC2, S3, VPC, IAM, and CloudWatch.</p></div>" +
            "<div class='card'><div class='card-icon'>🐧</div><h3>Linux Administration</h3><p>Managing Ubuntu servers, shell scripting, log analysis, and performance monitoring via CLI.</p></div>" +
            "<div class='card'><div class='card-icon'>🔁</div><h3>DevOps & CI/CD</h3><p>Automating deployments using GitHub Actions, Jenkins pipelines, and Docker containers.</p></div>" +
            "<div class='card'><div class='card-icon'>📊</div><h3>Monitoring & Alerts</h3><p>Setting up CloudWatch dashboards, alarms, and SNS notifications for real-time infrastructure visibility.</p></div>" +
            "<div class='card'><div class='card-icon'>🔐</div><h3>Cloud Security</h3><p>Implementing IAM roles, least-privilege policies, security groups, NACLs, and access management.</p></div>" +
            "<div class='card'><div class='card-icon'>🌐</div><h3>Networking</h3><p>Designing custom VPCs with public/private subnets, route tables, internet gateways, and DNS.</p></div>" +
            "</div></div>" +

            getFooter();

            sendResponse(exchange, html);
        }
    }

    // ── ABOUT PAGE ───────────────────────────────────────────────────────────
    static class AboutHandler implements HttpHandler {
        public void handle(HttpExchange exchange) throws IOException {
            String html = getHead("About") + getNav() +

            "<div class='section'>" +
            "<div class='section-title'>About <span>Me</span></div>" +
            "<div class='section-sub'>Cloud Engineer | Linux Admin | DevOps Enthusiast</div>" +

            "<div class='cards'>" +
            "<div class='card'>" +
            "<div class='card-icon'>👨‍💻</div>" +
            "<h3>Shiraz Siddiqui</h3>" +
            "<p>Aspiring AWS Cloud Engineer with hands-on experience in cloud infrastructure, Linux administration, and DevOps tools. AWS Certified Cloud Practitioner.</p>" +
            "<br><p>📍 Hyderabad, Telangana, India</p>" +
            "<br><p>📧 Shirazsiddiqui011@gmail.com</p>" +
            "<br><p>🐙 github.com/shiraz-cloud-engineer</p>" +
            "</div>" +
            "<div class='card'>" +
            "<div class='card-icon'>🎓</div>" +
            "<h3>Education</h3>" +
            "<p><strong>B.Tech – Electronics & Computer Engineering</strong><br>PES College of Engineering, Aurangabad<br>Graduated: 2025</p>" +
            "<br><p><strong>HSC – Science (PCM)</strong><br>Maulana Azad College, Aurangabad<br>2021 | 89%</p>" +
            "</div>" +
            "</div>" +

            "<div class='section-title' style='margin-top:40px'>Work <span>Experience</span></div>" +
            "<div class='section-sub'>Hands-on cloud engineering experience</div>" +
            "<div class='timeline'>" +
            "<div class='timeline-item'>" +
            "<div class='date'>June 2025 – September 2025</div>" +
            "<h4>Cloud Engineer Intern — Qualsoft, Aurangabad</h4>" +
            "<p>Provisioned AWS resources (EC2, S3, IAM, VPC, EBS), monitored Linux servers, configured CloudWatch alarms and dashboards, performed cloud operations and collaborated on infrastructure hardening.</p>" +
            "</div>" +
            "</div>" +

            "<div class='section-title' style='margin-top:40px'>🏆 <span>Certifications</span></div>" +
            "<div class='cards' style='margin-top:20px'>" +
            "<div class='card'><div class='card-icon'>🏅</div><h3>AWS Certified Cloud Practitioner</h3><p>Amazon Web Services (AWS) — Validates foundational knowledge of AWS Cloud services and architecture.</p></div>" +
            "</div>" +
            "</div>" +

            getFooter();

            sendResponse(exchange, html);
        }
    }

    // ── SKILLS PAGE ──────────────────────────────────────────────────────────
    static class SkillsHandler implements HttpHandler {
        public void handle(HttpExchange exchange) throws IOException {
            String html = getHead("Skills") + getNav() +

            "<div class='section'>" +
            "<div class='section-title'>Technical <span>Skills</span></div>" +
            "<div class='section-sub'>Tools and technologies I work with</div>" +

            "<div class='cards'>" +

            "<div class='card'><div class='card-icon'>☁️</div><h3>Cloud — AWS</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag'>EC2</span><span class='skill-tag'>S3</span><span class='skill-tag'>IAM</span>" +
            "<span class='skill-tag'>VPC</span><span class='skill-tag'>CloudWatch</span><span class='skill-tag'>EBS</span>" +
            "<span class='skill-tag'>Auto Scaling</span><span class='skill-tag'>Load Balancer</span><span class='skill-tag'>RDS</span><span class='skill-tag'>SNS</span>" +
            "</div></div>" +

            "<div class='card'><div class='card-icon'>🐧</div><h3>Operating Systems</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag blue'>Linux Ubuntu</span><span class='skill-tag blue'>Shell Scripting</span>" +
            "<span class='skill-tag blue'>Log Analysis</span><span class='skill-tag blue'>CLI</span>" +
            "<span class='skill-tag blue'>Windows Server</span>" +
            "</div></div>" +

            "<div class='card'><div class='card-icon'>🌐</div><h3>Networking</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag purple'>IP Addressing</span><span class='skill-tag purple'>DNS</span>" +
            "<span class='skill-tag purple'>HTTP/HTTPS</span><span class='skill-tag purple'>Subnets</span>" +
            "<span class='skill-tag purple'>Route Tables</span><span class='skill-tag purple'>Security Groups</span><span class='skill-tag purple'>NACLs</span>" +
            "</div></div>" +

            "<div class='card'><div class='card-icon'>🔁</div><h3>CI/CD & DevOps</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag green'>GitHub Actions</span><span class='skill-tag green'>Jenkins</span>" +
            "<span class='skill-tag green'>Docker</span><span class='skill-tag green'>Docker Compose</span>" +
            "<span class='skill-tag green'>Kubernetes (Basic)</span>" +
            "</div></div>" +

            "<div class='card'><div class='card-icon'>💻</div><h3>Scripting & Version Control</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag red'>Bash</span><span class='skill-tag red'>Python</span>" +
            "<span class='skill-tag red'>Git</span><span class='skill-tag red'>GitHub</span>" +
            "<span class='skill-tag red'>Webhooks</span>" +
            "</div></div>" +

            "<div class='card'><div class='card-icon'>📊</div><h3>Monitoring & Operations</h3><br>" +
            "<div class='skills-grid'>" +
            "<span class='skill-tag'>CloudWatch</span><span class='skill-tag'>SNS Alerts</span>" +
            "<span class='skill-tag'>Snapshot Management</span><span class='skill-tag'>Cost Optimization</span>" +
            "<span class='skill-tag'>Backup</span>" +
            "</div></div>" +

            "</div></div>" +
            getFooter();

            sendResponse(exchange, html);
        }
    }

    // ── PROJECTS PAGE ────────────────────────────────────────────────────────
    static class ProjectsHandler implements HttpHandler {
        public void handle(HttpExchange exchange) throws IOException {
            String html = getHead("Projects") + getNav() +

            "<div class='section'>" +
            "<div class='section-title'>My <span>Projects</span></div>" +
            "<div class='section-sub'>Hands-on AWS cloud projects built from scratch</div>" +
            "<div class='cards'>" +

            "<div class='card'><div class='card-icon'>🖥️</div>" +
            "<h3>EC2 Web Server Deployment</h3>" +
            "<p>Launched and configured an AWS EC2 instance with Nginx web server. Configured security groups for HTTP/HTTPS and SSH access.</p>" +
            "<br><p style='color:#666;font-size:13px;'>EC2 • Nginx • Security Groups • Linux</p></div>" +

            "<div class='card'><div class='card-icon'>📊</div>" +
            "<h3>CloudWatch Monitoring + Alarms</h3>" +
            "<p>Installed CloudWatch Agent on EC2 to stream logs and metrics. Created CPU alarms with SNS email notifications triggered by stress testing.</p>" +
            "<br><p style='color:#666;font-size:13px;'>CloudWatch • SNS • IAM • EC2</p></div>" +

            "<div class='card'><div class='card-icon'>🌐</div>" +
            "<h3>Static Website on EC2</h3>" +
            "<p>Hosted a fully functional static website on AWS EC2 using Apache2 and Java HTTP Server running on port 8080.</p>" +
            "<br><p style='color:#666;font-size:13px;'>EC2 • Apache2 • Java • Linux</p></div>" +

            "<div class='card'><div class='card-icon'>🗄️</div>" +
            "<h3>S3 Static Website Hosting</h3>" +
            "<p>Created and configured an S3 bucket for static website hosting with bucket policies for public access and IAM management.</p>" +
            "<br><p style='color:#666;font-size:13px;'>S3 • Bucket Policy • IAM</p></div>" +

            "<div class='card'><div class='card-icon'>🔒</div>" +
            "<h3>Custom VPC Network Setup</h3>" +
            "<p>Designed custom AWS VPC with public/private subnets, Internet Gateway, route tables, security groups, and NACLs.</p>" +
            "<br><p style='color:#666;font-size:13px;'>VPC • Subnets • IGW • Route Tables</p></div>" +

            "<div class='card'><div class='card-icon'>🔁</div>" +
            "<h3>CI/CD Pipeline — GitHub Actions</h3>" +
            "<p>Automated deployment pipeline using GitHub Actions with push-based triggers, YAML workflows, and secure SSH deployment to EC2.</p>" +
            "<br><p style='color:#666;font-size:13px;'>GitHub Actions • EC2 • SSH • YAML</p></div>" +

            "</div></div>" +
            getFooter();

            sendResponse(exchange, html);
        }
    }
}

