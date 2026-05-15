<script>
    import { onMount } from "svelte";
    let theme = $state("light");
    let mounted = $state(false);

    let mouseX = $state(0);
    let mouseY = $state(0);

    let heroEl = $state();
    let activeSection = $state("about");

    const sections = ["about", "skills", "experience", "projects", "contact"];

    const heroTransform = $derived(
        `perspective(1200px) rotateY(${mouseX * 2}deg) rotateX(${mouseY * -1.5}deg)`,
    );

    const cardTransform = $derived(
        `perspective(900px) rotateY(${mouseX * -14}deg) rotateX(${mouseY * 8}deg) translateZ(20px)`,
    );

    const contactTransform = $derived(
        `perspective(1000px) rotateY(${mouseX * 3}deg) rotateX(${mouseY * -2}deg)`,
    );

    const orb1Transform = $derived(
        `translate(${mouseX * -18}px, ${mouseY * -18}px)`,
    );

    const orb2Transform = $derived(
        `translate(${mouseX * 12}px, ${mouseY * 12}px)`,
    );

    const gridTransform = $derived(
        `perspective(800px) rotateX(${mouseY * 3}deg)`,
    );
    function toggleTheme() {
        theme = theme === "dark" ? "light" : "dark";

        localStorage.setItem("theme", theme);

        document.documentElement.classList.toggle("light", theme === "light");
    }
    onMount(() => {
        mounted = true;
        const savedTheme = localStorage.getItem("theme");

        if (savedTheme) {
            theme = savedTheme;
        }

        document.documentElement.classList.toggle("light", theme === "light");
        let frame;

        const handleMouse = (e) => {
            cancelAnimationFrame(frame);

            frame = requestAnimationFrame(() => {
                mouseX = (e.clientX / window.innerWidth - 0.5) * 2;
                mouseY = (e.clientY / window.innerHeight - 0.5) * 2;
            });
        };

        window.addEventListener("mousemove", handleMouse);

        const observer = new IntersectionObserver(
            (entries) => {
                entries.forEach((entry) => {
                    if (entry.isIntersecting) {
                        activeSection = entry.target.id;
                    }
                });
            },
            { threshold: 0.3 },
        );

        sections.forEach((s) => {
            const el = document.getElementById(s);

            if (el) {
                observer.observe(el);
            }
        });

        return () => {
            cancelAnimationFrame(frame);
            window.removeEventListener("mousemove", handleMouse);
            observer.disconnect();
        };
    });

    function tiltCard(e) {
        const card = e.currentTarget;

        const rect = card.getBoundingClientRect();

        const cx = rect.left + rect.width / 2;
        const cy = rect.top + rect.height / 2;

        const dx = (e.clientX - cx) / (rect.width / 2);
        const dy = (e.clientY - cy) / (rect.height / 2);

        card.style.transform = `
      perspective(600px)
      rotateY(${dx * 12}deg)
      rotateX(${-dy * 12}deg)
      translateZ(8px)
    `;
    }

    function resetCard(e) {
        e.currentTarget.style.transform =
            "perspective(600px) rotateY(0deg) rotateX(0deg) translateZ(0px)";
    }

    function scrollTo(id) {
        document.getElementById(id)?.scrollIntoView({
            behavior: "smooth",
        });
    }

    const skills = [
        {
            group: "Backend",
            items: ["Node.js", "Fastify", "Express.js", "Strapi CMS"],
            color: "cyan",
        },
        {
            group: "Database",
            items: [
                "PostgreSQL",
                "Query tuning",
                "Indexing strategy",
                "Schema design",
            ],
            color: "green",
        },
        {
            group: "Frontend",
            items: ["SvelteKit", "Tailwind CSS", "HTML5", "CSS3"],
            color: "violet",
        },
        {
            group: "Auth & Security",
            items: ["Keycloak", "RBAC", "JWT", "Secure APIs"],
            color: "amber",
        },
        {
            group: "DevOps",
            items: ["Docker", "Linux", "Nginx", "Bash scripting"],
            color: "pink",
        },
        {
            group: "Languages",
            items: ["JavaScript", "Java"],
            color: "cyan",
        },
    ];

    const projects = [
        {
            name: "Anganwadi Recruitment System",
            client: "ZP Pune – WCD",
            role: "Full-Stack Developer",
            desc: "District-level recruitment platform processing 10,000+ applications. Automated scrutiny, objection handling, and merit list generation.",
            stats: [
                "+70% less manual work",
                "+40% DB efficiency",
                "10k+ applications",
            ],
            stack: ["Node.js", "PostgreSQL", "Keycloak", "SvelteKit"],
            award: "Recommended for National e-Governance Award",
            color: "cyan",
        },
        {
            name: "Maharashtra State Board Website",
            client: "MSBSHSE",
            role: "Full-Stack Developer",
            desc: "Backend services with Strapi CMS and PostgreSQL for large-scale educational content delivery and course management.",
            stats: [
                "+30% query speed",
                "High-volume traffic",
                "Responsive frontend",
            ],
            stack: ["Strapi CMS", "PostgreSQL", "SvelteKit", "Tailwind"],
            color: "green",
        },
        {
            name: "Answer Sheet Verification",
            client: "State Board",
            role: "Backend Developer",
            desc: "High-throughput REST APIs using Fastify for reliable large-volume answer sheet data handling with RBAC.",
            stats: ["+35% API performance", "Keycloak RBAC", "Fastify APIs"],
            stack: ["Fastify", "PostgreSQL", "Keycloak"],
            color: "violet",
        },
        {
            name: "WAQF Board Website",
            client: "WAQF Board",
            role: "Backend Developer",
            desc: "CMS-driven website with Strapi and SvelteKit. Real-time content delivery and improved page load performance.",
            stats: ["Real-time delivery", "Strapi CMS", "Fast navigation"],
            stack: ["Strapi", "SvelteKit", "PostgreSQL"],
            color: "amber",
        },
    ];

    const colorMap = {
        cyan: {
            border: "#06b6d4",
            glow: "rgba(6,182,212,0.18)",
            text: "#67e8f9",
            badge: "rgba(6,182,212,0.12)",
        },

        green: {
            border: "#22c55e",
            glow: "rgba(34,197,94,0.18)",
            text: "#86efac",
            badge: "rgba(34,197,94,0.12)",
        },

        violet: {
            border: "#8b5cf6",
            glow: "rgba(139,92,246,0.18)",
            text: "#c4b5fd",
            badge: "rgba(139,92,246,0.12)",
        },

        amber: {
            border: "#f59e0b",
            glow: "rgba(245,158,11,0.18)",
            text: "#fcd34d",
            badge: "rgba(245,158,11,0.12)",
        },

        pink: {
            border: "#ec4899",
            glow: "rgba(236,72,153,0.18)",
            text: "#f9a8d4",
            badge: "rgba(236,72,153,0.12)",
        },
    };
</script>

<svelte:head>
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="" />
    <link
        href="https://fonts.googleapis.com/css2?family=Space+Mono:ital,wght@0,400;0,700;1,400&family=Syne:wght@400;500;700;800&display=swap"
        rel="stylesheet"
    />
    <title>Omkar Patil — Software Developer</title>
</svelte:head>

<div class="root" class:mounted>
    <!-- Grid background -->
    <div class="grid-bg" style={`transform:${gridTransform}`}></div>

    <!-- Floating orbs -->
    <div class="orb orb1" style={`transform:${orb1Transform}`}></div>
    <div class="orb orb2" style={`transform:${orb2Transform}`}></div>

    <!-- NAV -->
    <nav class="nav">
        <span class="nav-brand">OP<span class="blink">_</span></span>
        <ul class="nav-links">
            {#each sections as s}
                <li>
                    <button
                        class="nav-btn"
                        class:active={activeSection === s}
                        onclick={() => scrollTo(s)}
                    >
                        {s}
                    </button>
                </li>
            {/each}
        </ul>
        <button class="theme-toggle" onclick={toggleTheme}>
            {theme === "dark" ? "☀️" : "🌙"}
        </button>
        <a href="mailto:omkarpatil0424@gmail.com" class="nav-cta">hire me</a>
    </nav>

    <!-- HERO -->
    <section class="hero" bind:this={heroEl}>
        <div class="hero-inner" style={`transform:${heroTransform}`}>
            <div class="hero-eyebrow">
                <span class="dot"></span>
                <span>available for work · pune, india</span>
            </div>
            <h1 class="hero-name">
                <span class="line1">Omkar</span>
                <span class="line2">Patil<span class="accent">.</span></span>
            </h1>
            <p class="hero-role">
                Software Engineer · Backend &amp; e-Governance Systems
            </p>
            <p class="hero-desc">
                2+ years building scalable government platforms. Node.js,
                Fastify, PostgreSQL, Keycloak, SvelteKit. Delivered 3+ live
                e-Governance products.
            </p>
            <div class="hero-actions">
                <button
                    class="btn-primary"
                    onclick={() => scrollTo("projects")}
                >
                    <span>view projects</span>
                    <svg width="14" height="14" viewBox="0 0 14 14" fill="none"
                        ><path
                            d="M2 7h10M8 3l4 4-4 4"
                            stroke="currentColor"
                            stroke-width="1.5"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        /></svg
                    >
                </button>
                <a href="mailto:omkarpatil0424@gmail.com" class="btn-ghost"
                    >omkarpatil0424@gmail.com</a
                >
            </div>
            <div class="hero-stats">
                <div class="hstat">
                    <span class="hstat-n">2+</span><span class="hstat-l"
                        >years exp</span
                    >
                </div>
                <div class="hstat-div"></div>
                <div class="hstat">
                    <span class="hstat-n">4+</span><span class="hstat-l"
                        >live projects</span
                    >
                </div>
                <div class="hstat-div"></div>
                <div class="hstat">
                    <span class="hstat-n">10k+</span><span class="hstat-l"
                        >applications handled</span
                    >
                </div>
            </div>
        </div>

        <!-- 3D floating card -->
        <div class="hero-card" style={`transform:${cardTransform}`}>
            <div class="hcard-top">
                <span class="hcard-dot red"></span>
                <span class="hcard-dot yellow"></span>
                <span class="hcard-dot green"></span>
            </div>
            <pre class="hcard-code">{`// core stack
const engineer = {
  name: "Omkar Patil",
  role: "Software Engineer",
  company: "Chanakya Software",
  backend: ["Node.js","Fastify","Strapi"],
  db: ["PostgreSQL","Clickhouse"],
  frontend: ["SvelteKit","Tailwind"],
  exp: "2+ years",
  focus: "e-Governance",
}`}</pre>
        </div>
    </section>

    <!-- ABOUT -->
    <section id="about" class="section">
        <div class="section-label">about</div>
        <div class="about-grid">
            <div class="about-text">
                <h2 class="sec-h2">The developer<br />behind the systems.</h2>
                <p>
                    Software Engineer at <strong
                        >Chanakya Software Services</strong
                    > (Dec 2023 – Present), building scalable backend systems and
                    public-sector web applications for government clients across
                    Maharashtra.
                </p>
                <p>
                    My work centers on high-traffic platforms where reliability
                    isn't optional — recruitment portals, board websites, and
                    verification systems that serve tens of thousands of users.
                </p>
                <p>
                    B.E. Mechanical Engineering, Ashokrao Mane Group of
                    Institutions (2020) — transitioned fully into software
                    engineering and haven't looked back.
                </p>
                <div class="contact-pills">
                    <a href="tel:+917756805007" class="pill"
                        ><svg
                            width="13"
                            height="13"
                            viewBox="0 0 13 13"
                            fill="none"
                            ><path
                                d="M2 2h2.5l1 2.5L4 6a8 8 0 0 0 3 3l1.5-1.5L11 8.5V11a1 1 0 0 1-1 1A9 9 0 0 1 1 3a1 1 0 0 1 1-1z"
                                stroke="currentColor"
                                stroke-width="1.2"
                            /></svg
                        >+91 77568 05007</a
                    >
                    <a href="mailto:omkarpatil0424@gmail.com" class="pill"
                        ><svg
                            width="13"
                            height="13"
                            viewBox="0 0 13 13"
                            fill="none"
                            ><rect
                                x="1"
                                y="3"
                                width="11"
                                height="8"
                                rx="1"
                                stroke="currentColor"
                                stroke-width="1.2"
                            /><path
                                d="M1 3.5l5.5 4 5.5-4"
                                stroke="currentColor"
                                stroke-width="1.2"
                            /></svg
                        >omkarpatil0424@gmail.com</a
                    >
                    <span class="pill"
                        ><svg
                            width="13"
                            height="13"
                            viewBox="0 0 13 13"
                            fill="none"
                            ><path
                                d="M6.5 1a4 4 0 0 1 4 4c0 3-4 7-4 7S2.5 8 2.5 5a4 4 0 0 1 4-4z"
                                stroke="currentColor"
                                stroke-width="1.2"
                            /><circle
                                cx="6.5"
                                cy="5"
                                r="1.2"
                                stroke="currentColor"
                                stroke-width="1.2"
                            /></svg
                        >Pune, Maharashtra</span
                    >
                </div>
            </div>
            <div class="about-achievements">
                <div
                    class="achieve-card"
                    onmousemove={(e) => tiltCard(e, 0)}
                    onmouseleave={resetCard}
                >
                    <div class="achieve-icon">🏆</div>
                    <div class="achieve-label">National e-Governance Award</div>
                    <div class="achieve-sub">
                        Recommended by ZP Pune for Anganwadi system
                    </div>
                </div>
                <div
                    class="achieve-card"
                    onmousemove={(e) => tiltCard(e, 1)}
                    onmouseleave={resetCard}
                >
                    <div class="achieve-icon">⚡</div>
                    <div class="achieve-label">+30% DB speed improvement</div>
                    <div class="achieve-sub">
                        PostgreSQL indexing &amp; query restructuring
                    </div>
                </div>
                <div
                    class="achieve-card"
                    onmousemove={(e) => tiltCard(e, 2)}
                    onmouseleave={resetCard}
                >
                    <div class="achieve-icon">🎯</div>
                    <div class="achieve-label">Full Stack Java Dev</div>
                    <div class="achieve-sub">Certified course completion</div>
                </div>
                <div
                    class="achieve-card"
                    onmousemove={(e) => tiltCard(e, 3)}
                    onmouseleave={resetCard}
                >
                    <div class="achieve-icon">🚀</div>
                    <div class="achieve-label">3+ e-Gov platforms live</div>
                    <div class="achieve-sub">
                        Supporting high-volume public usage
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- SKILLS -->
    <section id="skills" class="section">
        <div class="section-label">skills</div>
        <h2 class="sec-h2">The stack.</h2>
        <div class="skills-grid">
            {#each skills as sk}
                {@const c = colorMap[sk.color]}
                <div
                    class="skill-card"
                    style="--border:{c.border};--glow:{c.glow};--badge:{c.badge};--text:{c.text}"
                    onmousemove={tiltCard}
                    onmouseleave={resetCard}
                >
                    <div class="skill-group">{sk.group}</div>
                    <div class="skill-tags">
                        {#each sk.items as item}
                            <span class="skill-tag">{item}</span>
                        {/each}
                    </div>
                </div>
            {/each}
        </div>
    </section>

    <!-- EXPERIENCE -->
    <section id="experience" class="section">
        <div class="section-label"> experience</div>
        <h2 class="sec-h2">Where I've worked.</h2>
        <div class="exp-block">
            <div class="exp-header">
                <div>
                    <div class="exp-company">
                        Chanakya Software Services Pvt. Ltd.
                    </div>
                    <div class="exp-role">Software Engineer</div>
                </div>
                <div class="exp-date">Dec 2023 – Present · Pune</div>
            </div>
            <ul class="exp-list">
                <li>
                    Engineered and maintained scalable backend services using <strong
                        >Node.js, Fastify</strong
                    >, and Strapi CMS.
                </li>
                <li>
                    Refined <strong>PostgreSQL</strong> schemas and indexing
                    strategy — improved data retrieval speed by
                    <strong>30%</strong>.
                </li>
                <li>
                    Configured secure authentication workflows using <strong
                        >Keycloak-based RBAC</strong
                    >.
                </li>
                <li>
                    Implemented responsive interfaces with <strong
                        >SvelteKit and Tailwind CSS</strong
                    >.
                </li>
                <li>
                    Delivered <strong
                        >3+ government e-Governance solutions</strong
                    > supporting high-volume public usage.
                </li>
            </ul>
        </div>
    </section>

    <!-- PROJECTS -->
    <section id="projects" class="section">
        <div class="section-label">projects</div>
        <h2 class="sec-h2">Things I've built.</h2>
        <div class="proj-grid">
            {#each projects as p}
                {@const c = colorMap[p.color]}
                <div
                    class="proj-card"
                    style="--border:{c.border};--glow:{c.glow};--badge:{c.badge};--text:{c.text}"
                    onmousemove={tiltCard}
                    onmouseleave={resetCard}
                >
                    {#if p.award}
                        <div class="proj-award">🏆 {p.award}</div>
                    {/if}
                    <div class="proj-meta">
                        <span class="proj-client">{p.client}</span>
                        <span class="proj-role-badge">{p.role}</span>
                    </div>
                    <div class="proj-name">{p.name}</div>
                    <p class="proj-desc">{p.desc}</p>
                    <div class="proj-stats">
                        {#each p.stats as s}
                            <span class="pstat">{s}</span>
                        {/each}
                    </div>
                    <div class="proj-stack">
                        {#each p.stack as t}
                            <span class="ptag">{t}</span>
                        {/each}
                    </div>
                </div>
            {/each}
        </div>
    </section>

    <!-- CONTACT -->
    <section id="contact" class="section contact-section">
        <div class="section-label">contact</div>
        <div class="contact-inner" style={`transform:${contactTransform}`}>
            <h2 class="contact-h2">
                Let's build something<br /><span class="accent">reliable.</span>
            </h2>
            <p class="contact-sub">
                Available for freelance, backend systems, e-governance
                platforms, and infrastructure work.
            </p>
            <div class="contact-cards">
                <a href="mailto:omkarpatil0424@gmail.com" class="ccard">
                    <span class="ccard-icon">✉</span>
                    <span class="ccard-label">Email</span>
                    <span class="ccard-val">omkarpatil0424@gmail.com</span>
                </a>
                <a href="tel:+917756805007" class="ccard">
                    <span class="ccard-icon">☎</span>
                    <span class="ccard-label">Phone</span>
                    <span class="ccard-val">+91 77568 05007</span>
                </a>
                <a
                    href="https://linkedin.com/in/omkar-patil"
                    target="_blank"
                    rel="noopener"
                    class="ccard"
                >
                    <span class="ccard-icon">in</span>
                    <span class="ccard-label">LinkedIn</span>
                    <span class="ccard-val">omkar-patil</span>
                </a>
                <div class="ccard">
                    <span class="ccard-icon">📍</span>
                    <span class="ccard-label">Location</span>
                    <span class="ccard-val">Pune, Maharashtra, India</span>
                </div>
            </div>
        </div>
    </section>

    <!-- FOOTER -->
    <footer class="footer">
        <span class="footer-mono">© 2025 Omkar Patil</span>
        <span class="footer-mono">Built with SvelteKit + Tailwind</span>
    </footer>
</div>

<style>
:global(*) {
  transition:
    background-color 0.25s ease,
    border-color 0.25s ease,
    color 0.25s ease;
}
:global(:root) {
  --bg: #070709;
  --bg-secondary: #0e0e12;

  --text: #e8e8e4;
  --text-muted: #a1a1aa;

  --border: rgba(255,255,255,0.07);

  --nav-bg: rgba(7,7,9,0.7);

  --card-bg: #0e0e12;

  --accent: #06b6d4;
}
:global(html.light) {
  --bg: #f5f7fb;

  --bg-secondary: #ffffff;

  --text: #111827;

  --text-muted: #4b5563;

  --border: rgba(0,0,0,0.08);

  --nav-bg: rgba(255,255,255,0.7);

  --card-bg: #ffffff;

  --accent: #0891b2;
}
    /* ── RESET & BASE ── */
    :global(*, *::before, *::after) {
        box-sizing: border-box;
        margin: 0;
        padding: 0;
    }
    :global(html) {
        scroll-behavior: smooth;
    }
    :global(body) {
        background: var(--bg);
    }

    .root {
        font-family: "Syne", sans-serif;
        background: var(--bg);
        color: var(--text);
        min-height: 100vh;
        overflow-x: hidden;
        opacity: 0;
        transition: opacity 0.6s ease;
    }
    .root.mounted {
        opacity: 1;
    }
    .theme-toggle {
  width: 42px;
  height: 42px;

  border-radius: 10px;

  border: 1px solid var(--border);

  background: var(--card-bg);

  color: var(--text);

  cursor: pointer;

  font-size: 16px;

  transition: all 0.2s ease;
}

.theme-toggle:hover {
  transform: translateY(-2px);

  border-color: var(--accent);
}

    /* ── BACKGROUND ── */
    .grid-bg {
        position: fixed;
        inset: 0;
        pointer-events: none;
        z-index: 0;
        background-image: linear-gradient(
                rgba(6, 182, 212, 0.04) 1px,
                transparent 1px
            ),
            linear-gradient(90deg, rgba(6, 182, 212, 0.04) 1px, transparent 1px);
        background-size: 48px 48px;
        transform: perspective(800px) rotateX(calc(var(--ry, 0) * 3deg));
        transition: transform 0.1s ease;
    }

    .orb {
        position: fixed;
        border-radius: 50%;
        pointer-events: none;
        z-index: 0;
        filter: blur(80px);
        transition: transform 0.15s ease;
    }
    .orb1 {
        width: 500px;
        height: 500px;
        background: radial-gradient(
            circle,
            rgba(6, 182, 212, 0.12),
            transparent 70%
        );
        top: -100px;
        left: -100px;
    }
    .orb2 {
        width: 400px;
        height: 400px;
        background: radial-gradient(
            circle,
            rgba(139, 92, 246, 0.1),
            transparent 70%
        );
        bottom: 0;
        right: -80px;
    }

    /* ── NAV ── */
    .nav {
        position: fixed;
        top: 0;
        left: 0;
        right: 0;
        z-index: 100;
        display: flex;
        align-items: center;
        gap: 2rem;
        padding: 1rem 2.5rem;
        background: rgba(7, 7, 9, 0.7);
        backdrop-filter: blur(16px);
        border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    }
    .nav-brand {
        font-family: "Space Mono", monospace;
        font-size: 15px;
        font-weight: 700;
        color: var(--accent);
        letter-spacing: 0.05em;
        margin-right: auto;
    }
    .blink {
        animation: blink 1.1s step-end infinite;
    }
    @keyframes blink {
        0%,
        100% {
            opacity: 1;
        }
        50% {
            opacity: 0;
        }
    }

    .nav-links {
        display: flex;
        list-style: none;
        gap: 0.25rem;
    }
    .nav-btn {
        background: none;
        border: none;
        color: #71717a;
        font-family: "Space Mono", monospace;
        font-size: 11px;
        padding: 6px 12px;
        border-radius: 6px;
        cursor: pointer;
        text-transform: lowercase;
        transition:
            color 0.2s,
            background 0.2s;
        letter-spacing: 0.04em;
    }
    .nav-btn:hover {
        color: var(--text);
        background: rgba(255, 255, 255, 0.05);
    }
    .nav-btn.active {
        color: var(--accent);
    }

    .nav-cta {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        background: #06b6d4;
        color: #000;
        padding: 7px 16px;
        border-radius: 6px;
        text-decoration: none;
        font-weight: 700;
        letter-spacing: 0.04em;
        transition: background 0.2s;
    }
    .nav-cta:hover {
        background: #22d3ee;
    }

    /* ── HERO ── */
    .hero {
        position: relative;
        z-index: 1;
        min-height: 100vh;
        display: grid;
        grid-template-columns: 1fr 420px;
        gap: 3rem;
        align-items: center;
        padding: 8rem 2.5rem 4rem;
        max-width: 1200px;
        margin: 0 auto;
    }

    .hero-inner {
        transition: transform 0.08s ease;
    }

    .hero-eyebrow {
        display: flex;
        align-items: center;
        gap: 8px;
        font-family: "Space Mono", monospace;
        font-size: 11px;
        color: #71717a;
        letter-spacing: 0.08em;
        margin-bottom: 1.5rem;
    }
    .dot {
        width: 7px;
        height: 7px;
        border-radius: 50%;
        background: #22c55e;
        box-shadow: 0 0 0 3px rgba(34, 197, 94, 0.2);
        animation: pulse 2s ease infinite;
    }
    @keyframes pulse {
        0%,
        100% {
            box-shadow: 0 0 0 3px rgba(34, 197, 94, 0.2);
        }
        50% {
            box-shadow: 0 0 0 6px rgba(34, 197, 94, 0.1);
        }
    }

    .hero-name {
        line-height: 1;
        margin-bottom: 1rem;
    }
    .line1 {
        display: block;
        font-size: clamp(52px, 7vw, 88px);
        font-weight: 800;
        color: var(--text);
        letter-spacing: -0.03em;
    }
    .line2 {
        display: block;
        font-size: clamp(52px, 7vw, 88px);
        font-weight: 800;
        color: var(--text);
        letter-spacing: -0.03em;
    }
    .accent {
        color: var(--accent);
    }

    .hero-role {
        font-family: "Space Mono", monospace;
        font-size: 12px;
        color: var(--accent);
        letter-spacing: 0.06em;
        margin-bottom: 1.25rem;
    }
    .hero-desc {
        font-size: 16px;
        color: var(--text-muted);
        line-height: 1.75;
        max-width: 500px;
        margin-bottom: 2rem;
    }

    .hero-actions {
        display: flex;
        flex-wrap: wrap;
        gap: 12px;
        margin-bottom: 2.5rem;
    }
    .btn-primary {
        display: flex;
        align-items: center;
        gap: 8px;
        background: #06b6d4;
        color: #000;
        border: none;
        font-family: "Syne", sans-serif;
        font-size: 14px;
        font-weight: 700;
        padding: 12px 24px;
        border-radius: 8px;
        cursor: pointer;
        transition:
            background 0.2s,
            transform 0.15s;
    }
    .btn-primary:hover {
        background: #22d3ee;
        transform: translateY(-2px);
    }
    .btn-ghost {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        color: #71717a;
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 8px;
        padding: 12px 18px;
        text-decoration: none;
        display: flex;
        align-items: center;
        transition:
            color 0.2s,
            border-color 0.2s;
    }
    .btn-ghost:hover {
        color: var(--text);
        border-color: rgba(255, 255, 255, 0.25);
    }

    .hero-stats {
        display: flex;
        align-items: center;
        gap: 1.5rem;
    }
    .hstat {
        display: flex;
        flex-direction: column;
    }
    .hstat-n {
        font-size: 24px;
        font-weight: 800;
        color: var(--accent);
        line-height: 1;
    }
    .hstat-l {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: #71717a;
        margin-top: 3px;
    }
    .hstat-div {
        width: 1px;
        height: 32px;
        background: rgba(255, 255, 255, 0.1);
    }

    /* 3D floating code card */
    .hero-card {
        background: var(--card-bg);
        border: 1px solid rgba(6, 182, 212, 0.2);
        border-radius: 14px;
        overflow: hidden;
        box-shadow:
            0 32px 80px rgba(0, 0, 0, 0.5),
            0 0 40px rgba(6, 182, 212, 0.06);
        transition: transform 0.1s ease;
        will-change: transform;
    }
    .hcard-top {
        display: flex;
        align-items: center;
        gap: 6px;
        padding: 10px 14px;
        background: #131318;
        border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    }
    .hcard-dot {
        width: 10px;
        height: 10px;
        border-radius: 50%;
    }
    .hcard-dot.red {
        background: #ef4444;
    }
    .hcard-dot.yellow {
        background: #eab308;
    }
    .hcard-dot.green {
        background: #22c55e;
    }
    .hcard-code {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        line-height: 1.75;
        color: var(--text-muted);
        padding: 1.25rem 1.5rem;
        white-space: pre;
        overflow-x: auto;
    }
    .hcard-code :global(.key) {
        color: #8b5cf6;
    }
    .hcard-code :global(.str) {
        color: #22c55e;
    }
    .hcard-code :global(.punct) {
        color: var(--accent);
    }

    /* ── SECTIONS ── */
    .section {
        position: relative;
        z-index: 1;
        max-width: 1200px;
        margin: 0 auto;
        padding: 5rem 2.5rem;
        border-top: 1px solid rgba(255, 255, 255, 0.05);
    }
    .section-label {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        color: var(--accent);
        letter-spacing: 0.1em;
        margin-bottom: 1.25rem;
    }
    .sec-h2 {
        font-size: clamp(28px, 4vw, 44px);
        font-weight: 800;
        line-height: 1.15;
        letter-spacing: -0.02em;
        margin-bottom: 2.5rem;
    }

    /* ABOUT */
    .about-grid {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 3rem;
        align-items: start;
    }
    .about-text p {
        font-size: 15px;
        color: var(--text-muted);
        line-height: 1.8;
        margin-bottom: 1rem;
    }
    .about-text strong {
        color: var(--text);
        font-weight: 500;
    }
    .contact-pills {
        display: flex;
        flex-wrap: wrap;
        gap: 8px;
        margin-top: 1.5rem;
    }
    .pill {
        display: flex;
        align-items: center;
        gap: 6px;
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: var(--text-muted);
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 100px;
        padding: 6px 12px;
        text-decoration: none;
        transition:
            color 0.2s,
            border-color 0.2s;
    }
    .pill:hover {
        color: var(--accent);
        border-color: rgba(6, 182, 212, 0.4);
    }
    .pill svg {
        flex-shrink: 0;
    }

    .about-achievements {
        display: grid;
        grid-template-columns: 1fr 1fr;
        gap: 12px;
    }
    .achieve-card {
        background: var(--card-bg);
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-radius: 12px;
        padding: 1.25rem;
        cursor: default;
        transition:
            transform 0.15s ease,
            box-shadow 0.15s ease,
            border-color 0.15s;
        will-change: transform;
    }
    .achieve-card:hover {
        border-color: rgba(6, 182, 212, 0.3);
        box-shadow:
            0 20px 40px rgba(0, 0, 0, 0.4),
            0 0 20px rgba(6, 182, 212, 0.06);
    }
    .achieve-icon {
        font-size: 22px;
        margin-bottom: 8px;
    }
    .achieve-label {
        font-size: 13px;
        font-weight: 700;
        color: var(--text);
        margin-bottom: 4px;
    }
    .achieve-sub {
        font-size: 11px;
        color: #71717a;
        line-height: 1.5;
    }

    /* SKILLS */
    .skills-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
        gap: 14px;
    }
    .skill-card {
        background: var(--card-bg);
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-radius: 12px;
        padding: 1.25rem;
        cursor: default;
        transition:
            transform 0.15s ease,
            border-color 0.2s,
            box-shadow 0.2s;
        will-change: transform;
    }
    .skill-card:hover {
        border-color: var(--border);
        box-shadow:
            0 12px 40px rgba(0, 0, 0, 0.4),
            0 0 24px var(--glow);
    }
    .skill-group {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: var(--text, #06b6d4);
        letter-spacing: 0.1em;
        text-transform: uppercase;
        margin-bottom: 12px;
    }
    .skill-tags {
        display: flex;
        flex-wrap: wrap;
        gap: 6px;
    }
    .skill-tag {
        font-size: 12px;
        background: var(--badge, rgba(6, 182, 212, 0.1));
        color: var(--text, #67e8f9);
        border: 1px solid var(--border, rgba(6, 182, 212, 0.2));
        border-radius: 6px;
        padding: 4px 10px;
    }

    /* EXPERIENCE */
    .exp-block {
        background: var(--card-bg);
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-left: 3px solid #06b6d4;
        border-radius: 0 12px 12px 0;
        padding: 2rem;
    }
    .exp-header {
        display: flex;
        justify-content: space-between;
        align-items: flex-start;
        flex-wrap: wrap;
        gap: 1rem;
        margin-bottom: 1.5rem;
    }
    .exp-company {
        font-size: 18px;
        font-weight: 700;
        color: var(--text);
    }
    .exp-role {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        color: var(--accent);
        margin-top: 4px;
        letter-spacing: 0.05em;
    }
    .exp-date {
        font-family: "Space Mono", monospace;
        font-size: 11px;
        color: #71717a;
        letter-spacing: 0.04em;
    }
    .exp-list {
        list-style: none;
        display: flex;
        flex-direction: column;
        gap: 10px;
    }
    .exp-list li {
        font-size: 14px;
        color: var(--text-muted);
        padding-left: 1.25rem;
        position: relative;
        line-height: 1.7;
    }
    .exp-list li::before {
        content: "›";
        position: absolute;
        left: 0;
        color: #22c55e;
        font-size: 16px;
        line-height: 1.4;
    }
    .exp-list li strong {
        color: var(--text);
        font-weight: 500;
    }

    /* PROJECTS */
    .proj-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
        gap: 16px;
    }
    .proj-card {
        background: var(--card-bg);
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-radius: 14px;
        padding: 1.5rem;
        display: flex;
        flex-direction: column;
        gap: 12px;
        cursor: default;
        transition:
            transform 0.15s ease,
            border-color 0.2s,
            box-shadow 0.2s;
        will-change: transform;
    }
    .proj-card:hover {
        border-color: var(--border);
        box-shadow:
            0 20px 50px rgba(0, 0, 0, 0.5),
            0 0 30px var(--glow);
    }
    .proj-award {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: #f59e0b;
        background: rgba(245, 158, 11, 0.1);
        border: 1px solid rgba(245, 158, 11, 0.25);
        border-radius: 6px;
        padding: 5px 10px;
        display: inline-block;
        letter-spacing: 0.04em;
    }
    .proj-meta {
        display: flex;
        align-items: center;
        gap: 8px;
        flex-wrap: wrap;
    }
    .proj-client {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: #71717a;
        letter-spacing: 0.04em;
    }
    .proj-role-badge {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        background: var(--badge);
        color: var(--text);
        border: 1px solid var(--border);
        border-radius: 100px;
        padding: 2px 8px;
        letter-spacing: 0.03em;
    }
    .proj-name {
        font-size: 16px;
        font-weight: 700;
        color: var(--text);
        line-height: 1.3;
    }
    .proj-desc {
        font-size: 13px;
        color: #71717a;
        line-height: 1.7;
        flex: 1;
    }
    .proj-stats {
        display: flex;
        flex-wrap: wrap;
        gap: 6px;
    }
    .pstat {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: var(--text);
        background: var(--badge);
        border: 1px solid var(--border);
        border-radius: 6px;
        padding: 3px 8px;
    }
    .proj-stack {
        display: flex;
        flex-wrap: wrap;
        gap: 5px;
    }
    .ptag {
        font-size: 11px;
        color: #52525b;
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-radius: 6px;
        padding: 3px 8px;
    }

    /* CONTACT */
    .contact-section {
        text-align: center;
    }
    .contact-inner {
        transition: transform 0.1s ease;
        display: inline-block;
        width: 100%;
    }
    .contact-h2 {
        font-size: clamp(32px, 5vw, 56px);
        font-weight: 800;
        letter-spacing: -0.03em;
        line-height: 1.1;
        margin-bottom: 1rem;
    }
    .contact-sub {
        font-size: 15px;
        color: #71717a;
        max-width: 480px;
        margin: 0 auto 2.5rem;
        line-height: 1.7;
    }
    .contact-cards {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
        gap: 12px;
        max-width: 800px;
        margin: 0 auto;
        text-align: left;
    }
    .ccard {
        background: var(--card-bg);
        border: 1px solid rgba(255, 255, 255, 0.07);
        border-radius: 12px;
        padding: 1.25rem;
        display: flex;
        flex-direction: column;
        gap: 6px;
        text-decoration: none;
        transition:
            border-color 0.2s,
            box-shadow 0.2s,
            transform 0.15s;
    }
    .ccard:hover {
        border-color: rgba(6, 182, 212, 0.35);
        box-shadow: 0 12px 40px rgba(0, 0, 0, 0.4);
        transform: translateY(-3px);
    }
    .ccard-icon {
        font-size: 18px;
        margin-bottom: 2px;
    }
    .ccard-label {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: #71717a;
        letter-spacing: 0.08em;
    }
    .ccard-val {
        font-size: 13px;
        color: var(--text);
        font-weight: 500;
        word-break: break-all;
    }

    /* FOOTER */
    .footer {
        position: relative;
        z-index: 1;
        display: flex;
        justify-content: space-between;
        padding: 1.5rem 2.5rem;
        border-top: 1px solid rgba(255, 255, 255, 0.05);
        max-width: 1200px;
        margin: 0 auto;
    }
    .footer-mono {
        font-family: "Space Mono", monospace;
        font-size: 10px;
        color: #3f3f46;
        letter-spacing: 0.06em;
    }

    /* ── RESPONSIVE ── */
    @media (max-width: 900px) {
        .hero {
            grid-template-columns: 1fr;
            padding-top: 6rem;
        }
        .hero-card {
            display: none;
        }
        .about-grid {
            grid-template-columns: 1fr;
        }
        .about-achievements {
            grid-template-columns: 1fr 1fr;
        }
        .nav-links {
            display: none;
        }
    }
    @media (max-width: 600px) {
        .hero {
            padding: 5rem 1.25rem 3rem;
        }
        .section {
            padding: 3rem 1.25rem;
        }
        .nav {
            padding: 0.875rem 1.25rem;
        }
        .footer {
            padding: 1.25rem;
        }
        .about-achievements {
            grid-template-columns: 1fr;
        }
    }

    /* ── ENTRANCE ANIMATIONS ── */
    .hero-inner {
        animation: fadeUp 0.8s ease 0.2s both;
    }
    .hero-card {
        animation: fadeUp 0.8s ease 0.4s both;
    }
    @keyframes fadeUp {
        from {
            opacity: 0;
            transform: translateY(24px);
        }
        to {
            opacity: 1;
        }
    }
</style>
