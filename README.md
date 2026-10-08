name: Build animated profile

on:
  push:
    paths:
      - ".github/workflows/build-profile.yml"
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Generate animated profile
        run: |
          python3 - <<'PYEOF'
          import os
          import random
          import math
          import xml.dom.minidom

          def esc(s):
              return (
                  str(s)
                  .replace("&", "&amp;")
                  .replace("<", "&lt;")
                  .replace(">", "&gt;")
                  .replace('"', "&quot;")
              )

          S = {}

          # ============================================================
          # HEADER
          # ============================================================

          random.seed(5)

          W, H = 1000, 340

          nodes = [
              (
                  random.randint(30, W - 30),
                  random.randint(20, H - 20)
              )
              for _ in range(26)
          ]

          h = [f'''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 {W} {H}"
               width="{W}"
               height="{H}"
               role="img"
               aria-label="Sohan Rana">

            <defs>

              <linearGradient id="bg"
                              x1="0"
                              y1="0"
                              x2="1"
                              y2="1">
                <stop offset="0" stop-color="#070d1a"/>
                <stop offset="0.55" stop-color="#0f2027"/>
                <stop offset="1" stop-color="#16303d"/>
              </linearGradient>

              <linearGradient id="shine"
                              gradientUnits="userSpaceOnUse"
                              x1="0"
                              y1="0"
                              x2="500"
                              y2="0"
                              spreadMethod="reflect">
                <stop offset="0" stop-color="#00d8ff"/>
                <stop offset="0.5" stop-color="#ff6ec7"/>
                <stop offset="1" stop-color="#f7df1e"/>

                <animateTransform
                    attributeName="gradientTransform"
                    type="translate"
                    from="0 0"
                    to="1000 0"
                    dur="7s"
                    repeatCount="indefinite"/>
              </linearGradient>

              <linearGradient id="trail"
                              x1="0"
                              y1="0"
                              x2="1"
                              y2="0">
                <stop offset="0" stop-color="#fff" stop-opacity="0"/>
                <stop offset="1" stop-color="#fff"/>
              </linearGradient>

              <linearGradient id="beam"
                              x1="0"
                              y1="0"
                              x2="1"
                              y2="0">
                <stop offset="0" stop-color="#fff" stop-opacity="0"/>
                <stop offset="0.5" stop-color="#fff" stop-opacity="0.14"/>
                <stop offset="1" stop-color="#fff" stop-opacity="0"/>
              </linearGradient>

              <filter id="blur40"
                      x="-50%"
                      y="-50%"
                      width="200%"
                      height="200%">
                <feGaussianBlur stdDeviation="40"/>
              </filter>

              <filter id="glow"
                      x="-20%"
                      y="-50%"
                      width="140%"
                      height="200%">
                <feGaussianBlur stdDeviation="8"/>
              </filter>

              <clipPath id="round">
                <rect width="{W}" height="{H}" rx="22"/>
              </clipPath>

              <style>
                .l {{
                  font: 900 90px 'Segoe UI','Helvetica Neue',Arial,sans-serif;
                  animation: wave 3.2s ease-in-out infinite;
                }}

                @keyframes wave {{
                  0%,60%,100% {{
                    transform: translateY(0);
                  }}

                  30% {{
                    transform: translateY(-14px);
                  }}
                }}

                .sub {{
                  font: 600 15px 'Segoe UI',Arial,sans-serif;
                  letter-spacing: 5px;
                  fill: #cfe3ec;
                }}

                .role {{
                  font: 600 22px 'Courier New',monospace;
                  fill: #7fe7ff;
                }}

                .pill {{
                  font: 700 11px 'Courier New',monospace;
                  letter-spacing: 2px;
                  fill: #d7e6ee;
                }}
              </style>

            </defs>

            <g clip-path="url(#round)">

              <rect width="{W}"
                    height="{H}"
                    fill="url(#bg)"/>

              <g filter="url(#blur40)" opacity="0.55">

                <ellipse cx="200"
                         cy="90"
                         rx="190"
                         ry="70"
                         fill="#00d8ff">

                  <animate
                      attributeName="cx"
                      values="200;480;200"
                      dur="14s"
                      repeatCount="indefinite"/>

                  <animate
                      attributeName="ry"
                      values="70;110;70"
                      dur="9s"
                      repeatCount="indefinite"/>

                </ellipse>

                <ellipse cx="720"
                         cy="250"
                         rx="220"
                         ry="70"
                         fill="#ff6ec7">

                  <animate
                      attributeName="cx"
                      values="720;420;720"
                      dur="16s"
                      repeatCount="indefinite"/>

                  <animate
                      attributeName="ry"
                      values="70;100;70"
                      dur="11s"
                      repeatCount="indefinite"/>

                </ellipse>

                <ellipse cx="860"
                         cy="60"
                         rx="150"
                         ry="60"
                         fill="#7a5cff">

                  <animate
                      attributeName="cx"
                      values="860;620;860"
                      dur="18s"
                      repeatCount="indefinite"/>

                </ellipse>

              </g>
          ''']

          # Network lines
          for i, a in enumerate(nodes):
              for j, b in enumerate(nodes):

                  if j > i and math.dist(a, b) < 135:

                      duration = round(random.uniform(3, 7), 1)
                      begin = round(random.uniform(0, 5), 1)

                      h.append(
                          f'''
                          <line
                              x1="{a[0]}"
                              y1="{a[1]}"
                              x2="{b[0]}"
                              y2="{b[1]}"
                              stroke="#9eefff"
                              stroke-width="1"
                              stroke-opacity="0.05">

                            <animate
                                attributeName="stroke-opacity"
                                values="0.04;0.38;0.04"
                                dur="{duration}s"
                                begin="{begin}s"
                                repeatCount="indefinite"/>

                          </line>
                          '''
                      )

          # Network nodes
          for x, y in nodes:

              duration = round(random.uniform(2.5, 5), 1)
              begin = round(random.uniform(0, 4), 1)

              h.append(
                  f'''
                  <circle
                      cx="{x}"
                      cy="{y}"
                      r="2"
                      fill="#d9fbff">

                    <animate
                        attributeName="r"
                        values="1.5;4;1.5"
                        dur="{duration}s"
                        begin="{begin}s"
                        repeatCount="indefinite"/>

                    <animate
                        attributeName="opacity"
                        values="0.35;1;0.35"
                        dur="{duration}s"
                        begin="{begin}s"
                        repeatCount="indefinite"/>

                  </circle>
                  '''
              )

          # Orbit
          h.append('''
          <path id="ring"
                d="M60 135
                   a440 34 0 1 0 880 0
                   a440 34 0 1 0 -880 0"
                fill="none"
                stroke="#ffffff"
                stroke-opacity="0.10"
                stroke-dasharray="3 9"/>

          <circle r="5" fill="#fff">
            <animateMotion
                dur="9s"
                repeatCount="indefinite">
              <mpath href="#ring"/>
            </animateMotion>
          </circle>

          <circle r="11"
                  fill="#00d8ff"
                  opacity="0.35">
            <animateMotion
                dur="9s"
                repeatCount="indefinite">
              <mpath href="#ring"/>
            </animateMotion>
          </circle>
          ''')

          # Shooting stars
          for y, begin in ((25, 1), (95, 4.5)):

              h.append(
                  f'''
                  <g opacity="0">

                    <line
                        x1="0"
                        y1="0"
                        x2="-90"
                        y2="-18"
                        stroke="url(#trail)"
                        stroke-width="2"
                        stroke-linecap="round"/>

                    <circle
                        r="2.5"
                        fill="#fff"/>

                    <animateTransform
                        attributeName="transform"
                        type="translate"
                        from="-100 {y}"
                        to="1100 {y + 170}"
                        dur="3.2s"
                        begin="{begin}s;{begin + 8}s"
                        repeatCount="indefinite"/>

                    <animate
                        attributeName="opacity"
                        values="0;1;1;0"
                        dur="3.2s"
                        begin="{begin}s;{begin + 8}s"
                        repeatCount="indefinite"/>

                  </g>
                  '''
              )

          # Name
          word = "SOHAN RANA"
          pitch = 70
          space = 38

          total = sum(
              space if c == " " else pitch
              for c in word
          )

          x = 500 - total / 2

          glow = []
          main = []

          for i, c in enumerate(word):

              width = space if c == " " else pitch

              if c != " ":

                  cx = x + width / 2

                  glow.append(
                      f'''
                      <text
                          class="l"
                          x="{cx:.0f}"
                          y="155"
                          text-anchor="middle"
                          fill="#00d8ff"
                          style="animation-delay:{i * 0.12:.2f}s">
                        {esc(c)}
                      </text>
                      '''
                  )

                  main.append(
                      f'''
                      <text
                          class="l"
                          x="{cx:.0f}"
                          y="155"
                          text-anchor="middle"
                          fill="url(#shine)"
                          style="animation-delay:{i * 0.12:.2f}s">
                        {esc(c)}
                      </text>
                      '''
                  )

              x += width

          h.append(
              '<g filter="url(#glow)" opacity="0.5">'
              + "".join(glow)
              + '''
              <animate
                  attributeName="opacity"
                  values="0.25;0.65;0.25"
                  dur="3.5s"
                  repeatCount="indefinite"/>
              </g>
              '''
          )

          h.append("".join(main))

          h.append('''
          <line
              x1="300"
              y1="182"
              x2="700"
              y2="182"
              stroke="url(#shine)"
              stroke-width="3"
              stroke-linecap="round"
              stroke-dasharray="400">

            <animate
                attributeName="stroke-dashoffset"
                values="400;0;0;400"
                keyTimes="0;0.35;0.8;1"
                dur="6s"
                repeatCount="indefinite"/>

          </line>

          <text
              class="sub"
              x="500"
              y="216"
              text-anchor="middle">
            CO-FOUNDER &amp; COO @ HONQIO IT
          </text>
          ''')

          # Rotating roles
          roles = [
              "AI & HCI Enthusiast",
              "Exploring NLP & AI Ethics",
              "Software Engineering Professional-in-Training",
              "Building human-centered technology"
          ]

          for i, role in enumerate(roles):

              width = len(role) * 13.2
              left = 500 - width / 2

              start = i * 0.25

              if start > 0:

                  key_times = [
                      0,
                      start,
                      start + 0.09,
                      start + 0.20,
                      start + 0.235,
                      1
                  ]

                  values = [
                      0,
                      0,
                      width,
                      width,
                      0,
                      0
                  ]

              else:

                  key_times = [0, 0.09, 0.20, 0.235, 1]
                  values = [0, width, width, 0, 0]

              kt = ";".join(
                  f"{v:.3f}"
                  for v in key_times
              )

              vs = ";".join(
                  f"{v:.0f}"
                  for v in values
              )

              h.append(
                  f'''
                  <clipPath id="roleClip{i}">
                    <rect
                        x="{left:.0f}"
                        y="238"
                        height="34"
                        width="0">

                      <animate
                          attributeName="width"
                          values="{vs}"
                          keyTimes="{kt}"
                          dur="18s"
                          repeatCount="indefinite"/>

                    </rect>
                  </clipPath>

                  <g clip-path="url(#roleClip{i})">
                    <text
                        class="role"
                        x="{left:.0f}"
                        y="262">
                      {esc(role)}
                    </text>
                  </g>
                  '''
              )

          # Status badges
          h.append('''
          <rect
              x="24"
              y="22"
              width="262"
              height="26"
              rx="13"
              fill="#ffffff"
              fill-opacity="0.07"
              stroke="#ffffff"
              stroke-opacity="0.18"/>

          <circle
              cx="42"
              cy="35"
              r="5"
              fill="#3fb950">

            <animate
                attributeName="opacity"
                values="1;0.2;1"
                dur="1.6s"
                repeatCount="indefinite"/>

          </circle>

          <text
              class="pill"
              x="58"
              y="39">
            OPEN TO INTERNSHIPS + RESEARCH
          </text>

          <rect
              x="850"
              y="22"
              width="126"
              height="26"
              rx="13"
              fill="#006a4e"
              fill-opacity="0.55"
              stroke="#ffffff"
              stroke-opacity="0.18"/>

          <circle
              cx="868"
              cy="35"
              r="6"
              fill="#f42a41">

            <animate
                attributeName="r"
                values="5;7.5;5"
                dur="2.4s"
                repeatCount="indefinite"/>

          </circle>

          <text
              class="pill"
              x="882"
              y="39">
            DHAKA, BD
          </text>

          </g>

          <rect
              x="1"
              y="1"
              width="998"
              height="338"
              rx="22"
              fill="none"
              stroke="url(#shine)"
              stroke-width="2"
              opacity="0.85"/>

          </svg>
          ''')

          S["header.svg"] = "\n".join(h)

          # ============================================================
          # TICKER
          # ============================================================

          words = (
              "AI  ·  HCI  ·  NLP  ·  AI ETHICS  ·  BANGLA NLP  ·  "
              "IOT  ·  ARDUINO  ·  CLOUD  ·  DEVOPS  ·  DATA SCIENCE  ·  "
              "CLIMATE TECH  ·  EXPLAINABLE AI  ·  "
          )

          S["ticker.svg"] = f'''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 1000 44"
               width="1000"
               height="44"
               role="img"
               aria-label="Interests ticker">

            <defs>

              <linearGradient
                  id="fade"
                  x1="0"
                  x2="1">

                <stop offset="0"
                      stop-color="#0b1220"/>

                <stop offset="0.08"
                      stop-color="#0b1220"
                      stop-opacity="0"/>

                <stop offset="0.92"
                      stop-color="#0b1220"
                      stop-opacity="0"/>

                <stop offset="1"
                      stop-color="#0b1220"/>

              </linearGradient>

              <linearGradient
                  id="tg"
                  gradientUnits="userSpaceOnUse"
                  x1="0"
                  x2="500"
                  spreadMethod="reflect">

                <stop offset="0" stop-color="#00d8ff"/>
                <stop offset="0.5" stop-color="#ff6ec7"/>
                <stop offset="1" stop-color="#f7df1e"/>

                <animateTransform
                    attributeName="gradientTransform"
                    type="translate"
                    from="0 0"
                    to="1000 0"
                    dur="6s"
                    repeatCount="indefinite"/>

              </linearGradient>

              <style>
                .t {{
                  font: 700 15px 'Courier New',monospace;
                  fill: url(#tg);
                }}
              </style>

            </defs>

            <rect
                width="1000"
                height="44"
                rx="10"
                fill="#0b1220"
                stroke="#ffffff"
                stroke-opacity="0.12"/>

            <g>

              <text
                  class="t"
                  x="0"
                  y="28"
                  textLength="1500"
                  lengthAdjust="spacing">
                {esc(words)}
              </text>

              <text
                  class="t"
                  x="1500"
                  y="28"
                  textLength="1500"
                  lengthAdjust="spacing">
                {esc(words)}
              </text>

              <animateTransform
                  attributeName="transform"
                  type="translate"
                  from="0 0"
                  to="-1500 0"
                  dur="30s"
                  repeatCount="indefinite"/>

            </g>

            <rect
                width="1000"
                height="44"
                rx="10"
                fill="url(#fade)"/>

          </svg>
          '''

          # ============================================================
          # TERMINAL
          # ============================================================

          lines = [
              ("$", "whoami"),
              ("", "sohan_rana  |  B.Sc. Software Engineering, DIU"),
              ("$", "cat mission.txt"),
              ("", "Build technology that is useful, responsible and people-centered."),
              ("$", "ls ~/research"),
              ("", "HCI/  AI-Ethics/  Bangla-NLP/  Human-Centered-AI/"),
              ("$", "./status --now"),
              ("", "Building Honqio IT  |  EarthLens @ NASA Space Apps 2026")
          ]

          terminal = [
              '''
              <svg xmlns="http://www.w3.org/2000/svg"
                   viewBox="0 0 800 380"
                   width="800"
                   height="380"
                   role="img"
                   aria-label="Terminal about card">

                <defs>

                  <style>
                    .m {
                      font: 15px 'Courier New', monospace;
                    }

                    .p {
                      fill: #7ee787;
                    }

                    .c {
                      fill: #e6edf3;
                    }

                    .o {
                      fill: #79c0ff;
                    }
                  </style>

                </defs>

                <rect
                    width="800"
                    height="380"
                    rx="14"
                    fill="#0d1117"
                    stroke="#30363d"/>

                <rect
                    width="800"
                    height="38"
                    rx="14"
                    fill="#161b22"/>

                <rect
                    y="24"
                    width="800"
                    height="14"
                    fill="#161b22"/>

                <circle cx="24" cy="19" r="6" fill="#ff5f56"/>
                <circle cx="46" cy="19" r="6" fill="#ffbd2e"/>
                <circle cx="68" cy="19" r="6" fill="#27c93f"/>

                <text
                    x="400"
                    y="24"
                    text-anchor="middle"
                    class="m"
                    fill="#8b949e"
                    font-size="13">
                  sohan@dhaka: ~/about
                </text>
              '''
          ]

          for i, (prefix, content) in enumerate(lines):

              y = 82 + i * 30 + (i // 2) * 8

              width = len(prefix + content) * 9.1 + 12

              start = 0.03 + i * 0.095
              end = start + 0.06

              terminal.append(
                  f'''
                  <clipPath id="terminalClip{i}">
                    <rect
                        x="28"
                        y="{y - 18}"
                        height="24"
                        width="0">

                      <animate
                          attributeName="width"
                          values="0;0;{width:.0f};{width:.0f};0"
                          keyTimes="0;{start:.3f};{end:.3f};0.95;1"
                          dur="22s"
                          repeatCount="indefinite"/>

                    </rect>
                  </clipPath>
                  '''
              )

          for i, (prefix, content) in enumerate(lines):

              y = 82 + i * 30 + (i // 2) * 8

              if prefix:

                  terminal.append(
                      f'''
                      <g
                          clip-path="url(#terminalClip{i})"
                          class="m">

                        <text
                            x="30"
                            y="{y}"
                            class="p">
                          $
                        </text>

                        <text
                            x="48"
                            y="{y}"
                            class="c">
                          {esc(content)}
                        </text>

                      </g>
                      '''
                  )

              else:

                  terminal.append(
                      f'''
                      <g
                          clip-path="url(#terminalClip{i})"
                          class="m">

                        <text
                            x="30"
                            y="{y}"
                            class="o">
                          {esc(content)}
                        </text>

                      </g>
                      '''
                  )

          yc = 82 + 8 * 30 + 4 * 8

          terminal.append(
              f'''
              <g class="m">

                <text
                    x="30"
                    y="{yc}"
                    class="p">
                  $
                </text>

                <rect
                    x="48"
                    y="{yc - 14}"
                    width="9"
                    height="18"
                    fill="#e6edf3">

                  <animate
                      attributeName="opacity"
                      values="1;0;1"
                      dur="1s"
                      repeatCount="indefinite"/>

                </rect>

              </g>

              </svg>
              '''
          )

          S["terminal.svg"] = "\n".join(terminal)

          # ============================================================
          # SCANNER
          # ============================================================

          D = 16

          scanner = [
              f'''
              <svg xmlns="http://www.w3.org/2000/svg"
                   viewBox="0 0 800 360"
                   width="800"
                   height="360"
                   role="img"
                   aria-label="BanglaJobGuard live scan animation">

                <defs>

                  <linearGradient
                      id="arc"
                      gradientUnits="userSpaceOnUse"
                      x1="-90"
                      y1="0"
                      x2="90"
                      y2="0">

                    <stop offset="0" stop-color="#3fb950"/>
                    <stop offset="0.5" stop-color="#f7df1e"/>
                    <stop offset="1" stop-color="#ff4d4d"/>

                  </linearGradient>

                  <linearGradient
                      id="scanline"
                      x1="0"
                      y1="0"
                      x2="1"
                      y2="0">

                    <stop
                        offset="0"
                        stop-color="#00d8ff"
                        stop-opacity="0"/>

                    <stop
                        offset="0.5"
                        stop-color="#00d8ff"/>

                    <stop
                        offset="1"
                        stop-color="#00d8ff"
                        stop-opacity="0"/>

                  </linearGradient>

                  <style>

                    .k {{
                      font: 13px 'Courier New',monospace;
                      fill: #c9d1d9;
                    }}

                    .ti {{
                      font: 700 16px 'Segoe UI',Arial,sans-serif;
                      fill: #fff;
                    }}

                    .hd {{
                      font: 700 12px 'Courier New',monospace;
                      letter-spacing: 3px;
                      fill: #6ea8c9;
                    }}

                    .v {{
                      font: 800 22px 'Segoe UI',Arial,sans-serif;
                      text-anchor: middle;
                    }}

                    .r {{
                      font: 12px 'Courier New',monospace;
                      text-anchor: middle;
                    }}

                  </style>

                </defs>

                <rect
                    width="800"
                    height="360"
                    rx="18"
                    fill="#0d1117"
                    stroke="#30363d"/>

                <text
                    class="hd"
                    x="24"
                    y="34">
                  BANGLAJOBGUARD // LIVE SCAN
                </text>

                <circle
                    cx="338"
                    cy="30"
                    r="4"
                    fill="#3fb950">

                  <animate
                      attributeName="opacity"
                      values="1;0.2;1"
                      dur="1.2s"
                      repeatCount="indefinite"/>

                </circle>

                <rect
                    x="24"
                    y="52"
                    width="410"
                    height="284"
                    rx="12"
                    fill="#161b22"
                    stroke="#30363d"/>
              '''
          ]

          ads = [
              (
                  "URGENT HIRING!!! Earn 80,000/month",
                  [
                      "Registration fee: 5,000 BDT",
                      "Contact: WhatsApp only, no company",
                      "Apply today or lose your seat!"
                  ],
                  "#ff4d4d"
              ),
              (
                  "Junior Software Engineer",
                  [
                      "Company: ABC Tech Ltd., Dhaka",
                      "Apply: official careers page",
                      "Salary: 35,000-45,000 BDT"
                  ],
                  "#3fb950"
              )
          ]

          for k, (title, items, color) in enumerate(ads):

              if k == 0:

                  opacity = '''
                  <animate
                      attributeName="opacity"
                      values="1;1;0;0"
                      keyTimes="0;0.48;0.5;1"
                      dur="16s"
                      repeatCount="indefinite"/>
                  '''

              else:

                  opacity = '''
                  <animate
                      attributeName="opacity"
                      values="0;0;1;1;0"
                      keyTimes="0;0.5;0.52;0.99;1"
                      dur="16s"
                      repeatCount="indefinite"/>
                  '''

              scanner.append(
                  f'''
                  <g opacity="{1 if k == 0 else 0}">

                    {opacity}

                    <text
                        class="ti"
                        x="40"
                        y="92">
                      {esc(title)}
                    </text>
                  '''
              )

              for j, item in enumerate(items):

                  y = 135 + j * 38

                  scanner.append(
                      f'''
                      <rect
                          x="34"
                          y="{y - 20}"
                          width="390"
                          height="30"
                          rx="7"
                          fill="{color}"
                          fill-opacity="0.32"/>

                      <text
                          class="k"
                          x="46"
                          y="{y}">
                        {esc(item)}
                      </text>
                      '''
                  )

              scanner.append('''
                    <rect
                        x="40"
                        y="262"
                        width="300"
                        height="8"
                        rx="4"
                        fill="#30363d"/>

                    <rect
                        x="40"
                        y="282"
                        width="360"
                        height="8"
                        rx="4"
                        fill="#30363d"/>

                    <rect
                        x="40"
                        y="302"
                        width="220"
                        height="8"
                        rx="4"
                        fill="#30363d"/>

                  </g>
              ''')

          scanner.append(f'''
                <rect
                    x="24"
                    y="70"
                    width="410"
                    height="3"
                    fill="url(#scanline)"
                    opacity="0">

                  <animate
                      attributeName="y"
                      values="70;70;280;70;70;280;280"
                      keyTimes="0;0.05;0.40;0.41;0.55;0.90;1"
                      dur="{D}s"
                      repeatCount="indefinite"/>

                  <animate
                      attributeName="opacity"
                      values="0;1;1;0;1;1;0"
                      keyTimes="0;0.05;0.40;0.41;0.55;0.90;1"
                      dur="{D}s"
                      repeatCount="indefinite"/>

                </rect>

                <g transform="translate(610 175)">

                  <path
                      d="M-90 0 A90 90 0 0 1 90 0"
                      fill="none"
                      stroke="#21262d"
                      stroke-width="18"
                      stroke-linecap="round"/>

                  <path
                      d="M-90 0 A90 90 0 0 1 90 0"
                      fill="none"
                      stroke="url(#arc)"
                      stroke-width="14"
                      stroke-linecap="round"/>

                  <g>

                    <line
                        x1="0"
                        y1="0"
                        x2="0"
                        y2="-74"
                        stroke="#fff"
                        stroke-width="4"
                        stroke-linecap="round"/>

                    <animateTransform
                        attributeName="transform"
                        type="rotate"
                        values="-90 0 0;-90 0 0;67 0 0;67 0 0;-90 0 0;-68 0 0;-68 0 0;-90 0 0"
                        keyTimes="0;0.05;0.40;0.48;0.55;0.90;0.99;1"
                        dur="{D}s"
                        repeatCount="indefinite"/>

                  </g>

                  <circle r="9" fill="#fff"/>
                  <circle r="4" fill="#0d1117"/>

                  <text
                      class="r"
                      x="-90"
                      y="24"
                      fill="#3fb950">
                    SAFE
                  </text>

                  <text
                      class="r"
                      x="90"
                      y="24"
                      fill="#ff6b6b">
                    RISK
                  </text>

                </g>

                <text
                    class="v"
                    x="610"
                    y="248"
                    fill="#ff6b6b">

                  SUSPICIOUS - 87%

                  <animate
                      attributeName="opacity"
                      values="0;0;1;1;0"
                      keyTimes="0;0.38;0.40;0.52;0.55"
                      dur="{D}s"
                      repeatCount="indefinite"/>

                </text>

                <text
                    class="v"
                    x="610"
                    y="248"
                    fill="#3fb950">

                  LIKELY LEGIT - 12%

                  <animate
                      attributeName="opacity"
                      values="0;0;1;1;0"
                      keyTimes="0;0.88;0.90;0.99;1"
                      dur="{D}s"
                      repeatCount="indefinite"/>

                </text>
          ''')

          scanner.append('''
                <text
                    class="r"
                    x="610"
                    y="278"
                    fill="#ff9b9b">
                  x  advance fee requested
                </text>

                <text
                    class="r"
                    x="610"
                    y="298"
                    fill="#ff9b9b">
                  x  no company identity
                </text>

                <text
                    class="r"
                    x="610"
                    y="318"
                    fill="#ff9b9b">
                  x  urgency pressure
                </text>

                <text
                    class="r"
                    x="610"
                    y="278"
                    fill="#7ee787"
                    opacity="0">
                  + named company
                </text>

                <text
                    class="r"
                    x="610"
                    y="298"
                    fill="#7ee787"
                    opacity="0">
                  + official apply channel
                </text>

                <text
                    class="r"
                    x="610"
                    y="318"
                    fill="#7ee787"
                    opacity="0">
                  + clear salary range
                </text>

              </svg>
          ''')

          S["scanner.svg"] = "\n".join(scanner)

          # ============================================================
          # EARTHLENS
          # ============================================================

          R = 105

          earth = [
              f'''
              <svg xmlns="http://www.w3.org/2000/svg"
                   viewBox="0 0 800 340"
                   width="800"
                   height="340"
                   role="img"
                   aria-label="EarthLens globe and trend animation">

                <defs>

                  <radialGradient
                      id="oc"
                      cx="0.35"
                      cy="0.3"
                      r="0.9">

                    <stop offset="0" stop-color="#2f8fd0"/>
                    <stop offset="0.6" stop-color="#0f4a7a"/>
                    <stop offset="1" stop-color="#06203a"/>

                  </radialGradient>

                  <filter id="gl">
                    <feGaussianBlur stdDeviation="6"/>
                  </filter>

                  <clipPath id="gc">
                    <circle r="{R}"/>
                  </clipPath>

                  <style>

                    .h {{
                      font: 700 12px 'Courier New',monospace;
                      letter-spacing: 3px;
                      fill: #6ea8c9;
                    }}

                    .s {{
                      font: 11px 'Courier New',monospace;
                      fill: #8b949e;
                    }}

                    .b {{
                      font: 700 12px 'Segoe UI',Arial,sans-serif;
                      fill: #fff;
                    }}

                  </style>

                </defs>

                <rect
                    width="800"
                    height="340"
                    rx="18"
                    fill="#0b1220"
                    stroke="#30363d"/>

                <text
                    class="h"
                    x="24"
                    y="34">
                  EARTHLENS // NASA EARTH DATA
                </text>

                <g transform="translate(170 185)">

                  <circle
                      r="{R + 6}"
                      fill="none"
                      stroke="#00d8ff"
                      stroke-width="6"
                      opacity="0.45"
                      filter="url(#gl)">

                    <animate
                        attributeName="opacity"
                        values="0.25;0.6;0.25"
                        dur="4s"
                        repeatCount="indefinite"/>

                  </circle>

                  <circle
                      r="{R}"
                      fill="url(#oc)"/>

                  <g
                      clip-path="url(#gc)"
                      fill="none"
                      stroke="#7fe7ff"
                      stroke-opacity="0.35">
              '''
          ]

          for latitude in (-60, -30, 0, 30, 60):

              y = R * math.sin(
                  math.radians(latitude)
              )

              width = R * math.cos(
                  math.radians(latitude)
              )

              earth.append(
                  f'''
                  <line
                      x1="{-width:.1f}"
                      y1="{y:.1f}"
                      x2="{width:.1f}"
                      y2="{y:.1f}"/>
                  '''
              )

          for i in range(6):

              values = ";".join(
                  f"{R * abs(math.cos(math.radians(a))):.1f}"
                  for a in range(0, 181, 15)
              )

              earth.append(
                  f'''
                  <ellipse
                      rx="{R}"
                      ry="{R}">

                    <animate
                        attributeName="rx"
                        values="{values}"
                        dur="12s"
                        begin="-{i * 2}s"
                        repeatCount="indefinite"/>

                  </ellipse>
                  '''
              )

          earth.append('''
                  </g>

                  <circle
                      r="105"
                      fill="none"
                      stroke="#fff"
                      stroke-opacity="0.15"/>

                  <g transform="translate(30 -22)">

                    <circle
                        r="4"
                        fill="#f42a41"/>

                    <circle
                        r="4"
                        fill="none"
                        stroke="#f42a41">

                      <animate
                          attributeName="r"
                          values="4;22;4"
                          dur="2.2s"
                          repeatCount="indefinite"/>

                      <animate
                          attributeName="opacity"
                          values="0.9;0;0.9"
                          dur="2.2s"
                          repeatCount="indefinite"/>

                    </circle>

                  </g>

                  <line
                      x1="42"
                      y1="-30"
                      x2="82"
                      y2="-70"
                      stroke="#fff"
                      stroke-opacity="0.6"/>

                  <text
                      class="b"
                      x="86"
                      y="-72">
                    BANGLADESH
                  </text>

                </g>

                <g>

                  <text
                      class="b"
                      x="400"
                      y="62">
                    Temperature &amp; rainfall trend
                  </text>

                  <text
                      class="s"
                      x="400"
                      y="80">
                    illustrative preview only
                  </text>

                  <line
                      x1="400"
                      y1="270"
                      x2="760"
                      y2="270"
                      stroke="#30363d"/>

                  <line
                      x1="400"
                      y1="100"
                      x2="400"
                      y2="270"
                      stroke="#30363d"/>
              ''')

          random.seed(10)

          points = [
              (
                  410 + i * 30,
                  222 - i * 6.2 + random.choice(
                      [-14, -8, 6, 12, -4, 10]
                  )
              )
              for i in range(12)
          ]

          path = "M" + " L".join(
              f"{x:.0f} {y:.0f}"
              for x, y in points
          )

          for i in range(12):

              height = random.randint(22, 70)
              x = 412 + i * 30

              earth.append(
                  f'''
                  <rect
                      x="{x}"
                      width="16"
                      rx="3"
                      fill="#2f8fd0"
                      fill-opacity="0.65"
                      y="270"
                      height="0">

                    <animate
                        attributeName="y"
                        values="270;{270-height};{270-height};270"
                        keyTimes="0;0.4;0.9;1"
                        dur="9s"
                        begin="{i * 0.18:.2f}s"
                        repeatCount="indefinite"/>

                    <animate
                        attributeName="height"
                        values="0;{height};{height};0"
                        keyTimes="0;0.4;0.9;1"
                        dur="9s"
                        begin="{i * 0.18:.2f}s"
                        repeatCount="indefinite"/>

                  </rect>
                  '''
              )

          earth.append(
              f'''
                  <path
                      id="trend"
                      d="{path}"
                      fill="none"
                      stroke="#ff6ec7"
                      stroke-width="3"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      pathLength="1"
                      stroke-dasharray="1"
                      stroke-dashoffset="1">

                    <animate
                        attributeName="stroke-dashoffset"
                        values="1;0;0;1"
                        keyTimes="0;0.5;0.9;1"
                        dur="9s"
                        repeatCount="indefinite"/>

                  </path>

                  <circle
                      r="6"
                      fill="#fff"
                      stroke="#ff6ec7"
                      stroke-width="3">

                    <animateMotion
                        dur="9s"
                        repeatCount="indefinite"
                        calcMode="linear">

                      <mpath href="#trend"/>

                    </animateMotion>

                  </circle>

                  <line
                      x1="420"
                      y1="296"
                      x2="446"
                      y2="296"
                      stroke="#ff6ec7"
                      stroke-width="3"/>

                  <text
                      class="s"
                      x="452"
                      y="300">
                    temperature
                  </text>

                  <rect
                      x="560"
                      y="291"
                      width="14"
                      height="10"
                      rx="2"
                      fill="#2f8fd0"/>

                  <text
                      class="s"
                      x="580"
                      y="300">
                    rainfall
                  </text>

                </g>

              </svg>
              '''
          )

          S["earthlens.svg"] = "\n".join(earth)

          # ============================================================
          # HONQIO PIPELINE
          # ============================================================

          pipeline_nodes = [
              ("CLOUD", 80),
              ("DEVOPS", 235),
              ("DATA", 390),
              ("AI", 545),
              ("IMPACT", 700)
          ]

          pipeline = ['''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 800 280"
               width="800"
               height="280"
               role="img"
               aria-label="Honqio IT pipeline animation">

            <defs>

              <linearGradient
                  id="fl"
                  gradientUnits="userSpaceOnUse"
                  x1="0"
                  x2="800">

                <stop offset="0" stop-color="#00d8ff"/>
                <stop offset="0.5" stop-color="#ff6ec7"/>
                <stop offset="1" stop-color="#f7df1e"/>

              </linearGradient>

              <style>

                .h {
                  font: 700 12px 'Courier New',monospace;
                  letter-spacing: 3px;
                  fill: #6ea8c9;
                }

                .n {
                  font: 700 13px 'Courier New',monospace;
                  letter-spacing: 2px;
                  fill: #e6edf3;
                  text-anchor: middle;
                }

                .tg {
                  font: italic 600 16px 'Segoe UI',Arial,sans-serif;
                  fill: #7fe7ff;
                  text-anchor: middle;
                }

              </style>

            </defs>

            <rect
                width="800"
                height="280"
                rx="18"
                fill="#0b1220"
                stroke="#30363d"/>

            <text
                class="h"
                x="24"
                y="34">
              HONQIO IT // PIPELINE
            </text>
          ''']

          for i in range(4):

              x1 = pipeline_nodes[i][1] + 56
              x2 = pipeline_nodes[i + 1][1] - 56

              pipeline.append(
                  f'''
                  <line
                      x1="{x1}"
                      y1="115"
                      x2="{x2}"
                      y2="115"
                      stroke="url(#fl)"
                      stroke-width="2.5"
                      stroke-dasharray="6 6">

                    <animate
                        attributeName="stroke-dashoffset"
                        values="0;-24"
                        dur="1s"
                        repeatCount="indefinite"/>

                  </line>

                  <circle
                      r="5"
                      fill="#fff">

                    <animateMotion
                        dur="2.4s"
                        begin="{i * 0.6}s"
                        repeatCount="indefinite"
                        path="M{x1} 115 L{x2} 115"/>

                  </circle>
                  '''
              )

          for name, x in pipeline_nodes:

              pipeline.append(
                  f'''
                  <g transform="translate({x} 115)">

                    <rect
                        x="-52"
                        y="-52"
                        width="104"
                        height="104"
                        rx="20"
                        fill="#111a2b"
                        stroke="#ffffff"
                        stroke-opacity="0.15"/>
                  '''
              )

              if name == "CLOUD":

                  pipeline.append('''
                    <g fill="#7fe7ff">

                      <circle cx="-14" cy="2" r="12"/>
                      <circle cx="4" cy="-8" r="17"/>
                      <circle cx="20" cy="4" r="12"/>
                      <rect x="-14" y="2" width="34" height="14"/>

                      <animateTransform
                          attributeName="transform"
                          type="translate"
                          values="0 0;0 -4;0 0"
                          dur="3s"
                          repeatCount="indefinite"/>

                    </g>
                  ''')

              elif name == "DEVOPS":

                  pipeline.append('''
                    <g>

                      <circle
                          r="17"
                          fill="none"
                          stroke="#ff6ec7"
                          stroke-width="9"
                          stroke-dasharray="7.1 7.1"/>

                      <circle
                          r="13"
                          fill="none"
                          stroke="#ff6ec7"
                          stroke-width="4"/>

                      <circle
                          r="5"
                          fill="#ff6ec7"/>

                      <animateTransform
                          attributeName="transform"
                          type="rotate"
                          from="0"
                          to="360"
                          dur="8s"
                          repeatCount="indefinite"/>

                    </g>
                  ''')

              elif name == "DATA":

                  pipeline.append('''
                    <g fill="#f7df1e">

                      <rect
                          x="-20"
                          y="-16"
                          width="40"
                          height="30"/>

                      <ellipse
                          cx="0"
                          cy="14"
                          rx="20"
                          ry="7"/>

                      <ellipse
                          cx="0"
                          cy="-16"
                          rx="20"
                          ry="7"
                          fill="#fff3a0"/>

                    </g>
                  ''')

              elif name == "AI":

                  ai_points = [
                      (-22, -10),
                      (-8, 14),
                      (8, -14),
                      (22, 10),
                      (0, 0)
                  ]

                  for a in range(len(ai_points)):

                      for b in range(a + 1, len(ai_points)):

                          pipeline.append(
                              f'''
                              <line
                                  x1="{ai_points[a][0]}"
                                  y1="{ai_points[a][1]}"
                                  x2="{ai_points[b][0]}"
                                  y2="{ai_points[b][1]}"
                                  stroke="#9eefff"
                                  stroke-opacity="0.5"/>
                              '''
                          )

                  for k, (px, py) in enumerate(ai_points):

                      pipeline.append(
                          f'''
                          <circle
                              cx="{px}"
                              cy="{py}"
                              r="5"
                              fill="#00d8ff">

                            <animate
                                attributeName="r"
                                values="3;7;3"
                                dur="{1.6 + k * 0.3:.1f}s"
                                repeatCount="indefinite"/>

                          </circle>
                          '''
                      )

              else:

                  pipeline.append('''
                    <g>

                      <polygon
                          points="0,-24 7,-8 24,-7 11,4 15,22 0,13 -15,22 -11,4 -24,-7 -7,-8"
                          fill="#3fb950">

                        <animateTransform
                            attributeName="transform"
                            type="scale"
                            values="1;1.18;1"
                            dur="2s"
                            repeatCount="indefinite"/>

                      </polygon>

                    </g>

                    <circle
                        r="26"
                        fill="none"
                        stroke="#3fb950">

                      <animate
                          attributeName="r"
                          values="26;50;26"
                          dur="2s"
                          repeatCount="indefinite"/>

                      <animate
                          attributeName="opacity"
                          values="0.8;0;0.8"
                          dur="2s"
                          repeatCount="indefinite"/>

                    </circle>
                  ''')

              pipeline.append(
                  f'''
                    <text
                        class="n"
                        y="76">
                      {name}
                    </text>

                  </g>
                  '''
              )

          pipeline.append('''
            <text
                class="tg"
                x="400"
                y="250">

              Your partner in progress.

              <animate
                  attributeName="opacity"
                  values="0.5;1;0.5"
                  dur="3s"
                  repeatCount="indefinite"/>

            </text>

          </svg>
          ''')

          S["pipeline.svg"] = "\n".join(pipeline)

          # ============================================================
          # TECH ORBIT
          # ============================================================

          orbits = [
              (
                  95,
                  18,
                  1,
                  [
                      ("Python", "#3776AB", "#fff"),
                      ("JavaScript", "#F7DF1E", "#111"),
                      ("C", "#00599C", "#fff")
                  ]
              ),
              (
                  155,
                  32,
                  -1,
                  [
                      ("React", "#20232A", "#61DAFB"),
                      ("Node.js", "#339933", "#fff"),
                      ("MySQL", "#4479A1", "#fff"),
                      ("Pandas", "#150458", "#fff")
                  ]
              ),
              (
                  215,
                  48,
                  1,
                  [
                      ("Arduino", "#00979D", "#fff"),
                      ("Git", "#F05032", "#fff"),
                      ("Docker", "#2496ED", "#fff"),
                      ("AWS", "#FF9900", "#111"),
                      ("Linux", "#FCC624", "#111")
                  ]
              )
          ]

          orbit = ['''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 800 500"
               width="800"
               height="500"
               role="img"
               aria-label="Tech stack orbit">

            <defs>

              <radialGradient id="sun">

                <stop offset="0" stop-color="#fff"/>
                <stop offset="0.35" stop-color="#ff6ec7"/>
                <stop offset="1" stop-color="#00d8ff"/>

              </radialGradient>

              <filter id="nb">
                <feGaussianBlur stdDeviation="45"/>
              </filter>

            </defs>

            <rect
                width="800"
                height="500"
                rx="20"
                fill="#0b1220"/>

            <g
                filter="url(#nb)"
                opacity="0.5">

              <ellipse
                  cx="160"
                  cy="120"
                  rx="140"
                  ry="80"
                  fill="#7a5cff"/>

              <ellipse
                  cx="650"
                  cy="390"
                  rx="160"
                  ry="80"
                  fill="#00d8ff"/>

            </g>
          ''']

          random.seed(3)

          for _ in range(60):

              x = random.randint(10, 790)
              y = random.randint(10, 490)

              duration = round(
                  random.uniform(1.5, 4),
                  1
              )

              radius = random.choice(
                  [0.8, 1, 1.4]
              )

              begin = round(
                  random.uniform(0, 3),
                  1
              )

              orbit.append(
                  f'''
                  <circle
                      cx="{x}"
                      cy="{y}"
                      r="{radius}"
                      fill="#fff">

                    <animate
                        attributeName="opacity"
                        values="0.1;1;0.1"
                        dur="{duration}s"
                        begin="{begin}s"
                        repeatCount="indefinite"/>

                  </circle>
                  '''
              )

          orbit.append('''
            <g transform="translate(400 250)">
          ''')

          for radius, _, _, _ in orbits:

              orbit.append(
                  f'''
                  <circle
                      r="{radius}"
                      fill="none"
                      stroke="#6ea8c9"
                      stroke-opacity="0.3"
                      stroke-dasharray="4 8"/>
                  '''
              )

          orbit.append('''
              <circle
                  r="58"
                  fill="none"
                  stroke="#ff6ec7"
                  stroke-width="3"
                  stroke-dasharray="2 10"
                  stroke-linecap="round">

                <animateTransform
                    attributeName="transform"
                    type="rotate"
                    from="0"
                    to="360"
                    dur="10s"
                    repeatCount="indefinite"/>

              </circle>

              <circle
                  r="40"
                  fill="url(#sun)"/>

              <text
                  y="9"
                  text-anchor="middle"
                  style="font:900 26px Arial,sans-serif"
                  fill="#0b1220">
                AI
              </text>
          ''')

          for radius, duration, direction, items in orbits:

              count = len(items)

              for k, (label, bg_color, fg_color) in enumerate(items):

                  angle = 360 / count * k

                  start = angle
                  end = (
                      angle + 360
                      if direction > 0
                      else angle - 360
                  )

                  width = len(label) * 8.6 + 20

                  orbit.append(
                      f'''
                      <g>

                        <animateTransform
                            attributeName="transform"
                            type="rotate"
                            from="{start}"
                            to="{end}"
                            dur="{duration}s"
                            repeatCount="indefinite"/>

                        <g transform="translate({radius} 0)">

                          <g>

                            <animateTransform
                                attributeName="transform"
                                type="rotate"
                                from="{-start}"
                                to="{-end}"
                                dur="{duration}s"
                                repeatCount="indefinite"/>

                            <rect
                                x="{-width / 2:.0f}"
                                y="-14"
                                width="{width:.0f}"
                                height="28"
                                rx="14"
                                fill="{bg_color}"
                                stroke="#ffffff"
                                stroke-opacity="0.35"/>

                            <text
                                y="5"
                                text-anchor="middle"
                                style="font:700 14px 'Segoe UI',Arial,sans-serif"
                                fill="{fg_color}">
                              {esc(label)}
                            </text>

                          </g>

                        </g>

                      </g>
                      '''
                  )

          orbit.append('''
            </g>

            <text
                x="24"
                y="36"
                style="font:700 14px 'Courier New',monospace;letter-spacing:3px"
                fill="#6ea8c9">
              TECH ORBIT
            </text>

          </svg>
          ''')

          S["orbit.svg"] = "\n".join(orbit)

          # ============================================================
          # DIVIDER
          # ============================================================

          S["divider.svg"] = '''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 1000 50"
               width="1000"
               height="50"
               role="presentation">

            <defs>

              <linearGradient
                  id="dividerGradient"
                  gradientUnits="userSpaceOnUse"
                  x1="0"
                  x2="500"
                  spreadMethod="reflect">

                <stop offset="0" stop-color="#00d8ff"/>
                <stop offset="0.5" stop-color="#ff6ec7"/>
                <stop offset="1" stop-color="#f7df1e"/>

                <animateTransform
                    attributeName="gradientTransform"
                    type="translate"
                    from="0 0"
                    to="1000 0"
                    dur="6s"
                    repeatCount="indefinite"/>

              </linearGradient>

            </defs>

            <path
                fill="none"
                stroke="url(#dividerGradient)"
                stroke-width="3"
                stroke-linecap="round"
                d="M0 25 C125 5 250 45 375 25 S625 5 750 25 S875 45 1000 25">

              <animate
                  attributeName="d"
                  dur="4s"
                  repeatCount="indefinite"
                  values="
                  M0 25 C125 5 250 45 375 25 S625 5 750 25 S875 45 1000 25;
                  M0 25 C125 45 250 5 375 25 S625 45 750 25 S875 5 1000 25;
                  M0 25 C125 5 250 45 375 25 S625 5 750 25 S875 45 1000 25"/>

            </path>

            <circle
                r="6"
                fill="#fff">

              <animateMotion
                  dur="5s"
                  repeatCount="indefinite"
                  path="M0 25 L1000 25"/>

            </circle>

          </svg>
          '''

          # ============================================================
          # FOOTER
          # ============================================================

          S["footer.svg"] = '''
          <svg xmlns="http://www.w3.org/2000/svg"
               viewBox="0 0 1000 160"
               width="1000"
               height="160"
               role="img"
               aria-label="Thanks for visiting">

            <defs>

              <linearGradient
                  id="footerGradient"
                  x1="0"
                  y1="0"
                  x2="1"
                  y2="1">

                <stop offset="0" stop-color="#2c5364"/>
                <stop offset="1" stop-color="#0f2027"/>

              </linearGradient>

            </defs>

            <path
                fill="#00d8ff"
                fill-opacity="0.25"
                d="M0 70 C200 30 300 110 500 70 S800 30 1000 70 V160 H0 Z">

              <animate
                  attributeName="d"
                  dur="7s"
                  repeatCount="indefinite"
                  values="
                  M0 70 C200 30 300 110 500 70 S800 30 1000 70 V160 H0 Z;
                  M0 70 C200 110 300 30 500 70 S800 110 1000 70 V160 H0 Z;
                  M0 70 C200 30 300 110 500 70 S800 30 1000 70 V160 H0 Z"/>

            </path>

            <path
                fill="#ff6ec7"
                fill-opacity="0.22"
                d="M0 85 C250 55 350 115 550 85 S850 55 1000 85 V160 H0 Z">

              <animate
                  attributeName="d"
                  dur="9s"
                  repeatCount="indefinite"
                  values="
                  M0 85 C250 55 350 115 550 85 S850 55 1000 85 V160 H0 Z;
                  M0 85 C250 115 350 55 550 85 S850 115 1000 85 V160 H0 Z;
                  M0 85 C250 55 350 115 550 85 S850 55 1000 85 V160 H0 Z"/>

            </path>

            <path
                fill="url(#footerGradient)"
                d="M0 105 C200 80 400 130 600 105 S850 85 1000 105 V160 H0 Z">

              <animate
                  attributeName="d"
                  dur="11s"
                  repeatCount="indefinite"
                  values="
                  M0 105 C200 80 400 130 600 105 S850 85 1000 105 V160 H0 Z;
                  M0 105 C200 130 400 80 600 105 S850 125 1000 105 V160 H0 Z;
                  M0 105 C200 80 400 130 600 105 S850 85 1000 105 V160 H0 Z"/>

            </path>

            <text
                x="500"
                y="138"
                text-anchor="middle"
                style="font:700 22px 'Segoe UI',Arial,sans-serif;letter-spacing:3px"
                fill="#fff">

              THANKS FOR VISITING

              <animate
                  attributeName="opacity"
                  values="0.6;1;0.6"
                  dur="3s"
                  repeatCount="indefinite"/>

            </text>

          </svg>
          '''

          # ============================================================
          # WRITE SVG FILES
          # ============================================================

          os.makedirs("assets", exist_ok=True)

          for filename, content in S.items():

              # Validate XML before writing
              xml.dom.minidom.parseString(content)

              path = os.path.join(
                  "assets",
                  filename
              )

              with open(
                  path,
                  "w",
                  encoding="utf-8"
              ) as file:

                  file.write(content)

              print(f"wrote {path}")

          # ============================================================
          # README
          # ============================================================

          README = r'''
          <div align="center">

          <img src="assets/header.svg" width="100%" alt="Sohan Rana"/>

          <img src="assets/ticker.svg" width="100%" alt="AI · HCI · NLP · AI Ethics · Bangla NLP · IoT · Cloud · DevOps"/>

          <br/>

          📍 Dhaka, Bangladesh &nbsp;·&nbsp;
          🌐 [Portfolio](https://sohan-rana.github.io/SOHAN/) &nbsp;·&nbsp;
          💼 [LinkedIn](https://www.linkedin.com/in/sohan-r-54729a184/) &nbsp;·&nbsp;
          ✉️ [Email](mailto:sohanrana243@gmail.com) &nbsp;·&nbsp;
          🎓 [Google Scholar](https://scholar.google.com/citations?user=706NRN8AAAAJ&hl=en) &nbsp;·&nbsp;
          🔬 [ResearchGate](https://www.researchgate.net/profile/Md-Sohan-Rana)

          <br/><br/>

          <img
              src="https://komarev.com/ghpvc/?username=Sohan-Rana&label=Profile%20Views&color=2c5364&style=flat-square"
              alt="Profile views"/>

          </div>

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 💡 About

          <div align="center">

          <img
              src="assets/terminal.svg"
              width="80%"
              alt="Terminal about card"/>

          </div>

          <br/>

          I am a **Software Engineering professional-in-training** with a growing focus on Artificial Intelligence, Human-Computer Interaction, Natural Language Processing and responsible technology.

          My interests lie at the intersection of technology and human needs: how intelligent systems can be useful, accessible, ethical and meaningful.

          My journey began with software development and practical problem-solving, and has expanded toward research-driven AI and human-centered computing. My work spans applied research, IoT-based systems and NLP.

          I am also the **Co-founder & COO of Honqio IT**, a technology startup working in Cloud Computing, AI, DevOps and Data Science.

          ```python
          from dataclasses import dataclass


          @dataclass(frozen=True)
          class Engineer:
              name: str = "Sohan Rana"
              location: str = "Dhaka, Bangladesh"
              status: str = "Software Engineering professional-in-training"
              education: str = "B.Sc. Software Engineering, Daffodil International University"
              role: str = "Co-founder & COO, Honqio IT"
              research: tuple = (
                  "AI",
                  "HCI",
                  "NLP",
                  "AI Ethics",
                  "Human-Centered AI"
              )
              languages: tuple = (
                  "Bangla (native)",
                  "English (intermediate-advanced)"
              )
              mission: str = (
                  "Build technology that is innovative, responsible, "
                  "inclusive and human-centered."
              )
          ```

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 🚀 Current Focus

          | Area | Details |
          |:--|:--|
          | **Research** | HCI, AI Ethics, Bangla NLP, human-centered AI, fraud detection |
          | **Project** | BanglaJobGuard: explainable hybrid AI for detecting fraudulent job ads in Bangladesh |
          | **Competition** | NASA Space Apps Challenge 2026, EarthLens |
          | **Startup** | Building Honqio IT |
          | **Learning** | Machine Learning, NLP, Data Science, AWS, Azure, Google Cloud, DevOps, Docker, Linux, ESP32 |
          | **Open to** | Internships, research collaboration, AI and data science projects |

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 🔥 Featured Projects

          ### 🛡️ BanglaJobGuard

          Explainable hybrid AI for detecting fraudulent and suspicious job advertisements in the Bangla job market.

          `Python` `NLP` `Explainable AI`

          <div align="center">

          <img
              src="assets/scanner.svg"
              width="80%"
              alt="BanglaJobGuard live scan animation"/>

          </div>

          ### 🌍 EarthLens

          Bangladesh Environmental Trend Detective: turns NASA Earth observation data into accessible visual insights about temperature and rainfall trends.

          `Chart.js` `Leaflet.js` `Python` `Pandas`

          <div align="center">

          <img
              src="assets/earthlens.svg"
              width="80%"
              alt="EarthLens animation"/>

          </div>

          ### 🌾 Smart Field Alert System

          Arduino-based agricultural intrusion detection using a PIR HC-SR501 sensor, built to help protect farmland with low-cost hardware.

          `Arduino` `IoT` `Sensors`

          **Other projects:**

          - Honqio IT Website
          - DIU Bus Service Automation
          - Real Estate Management System
          - Blood Donation & Emergency Matching App

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 🔬 Research

          **Smart Field Alert System Using Arduino for Agricultural Intrusion Detection**

          Published research:
          [ResearchGate](https://www.researchgate.net/publication/404114451_Smart_Field_Alert_System_Using_Arduino_for_Agricultural_Intrusion_Detection)

          **Current interests:**

          - Human-Computer Interaction
          - AI Ethics and Responsible AI
          - Natural Language Processing
          - Bangla NLP
          - Human-Centered AI
          - AI in employment and recruitment
          - Climate and agricultural technology

          ## 🎓 Education

          - **Daffodil International University** — B.Sc. in Software Engineering
          - **Dhaka Imperial College** — HSC, Science
          - **Motijheel Model School & College** — SSC, Science

          ## 🌐 Conferences & Global Engagements

          - **RCOY APAC 2026**
          - **NASA Space Apps Challenge 2026**
          - Youth forums, workshops and collaborative initiatives on technology, climate and society

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 🏢 Honqio IT

          **Co-founder & COO**

          A technology startup focused on Cloud Computing, AI, DevOps and Data Science, delivering practical digital solutions for individuals and organizations.

          Website:
          [honqioit.netlify.app](https://honqioit.netlify.app)

          <div align="center">

          <img
              src="assets/pipeline.svg"
              width="80%"
              alt="Honqio IT pipeline animation"/>

          </div>

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## ⚙️ Tech Stack

          <div align="center">

          <img
              src="assets/orbit.svg"
              width="85%"
              alt="Animated tech stack orbit"/>

          </div>

          **Currently learning:**

          `Java` `AWS` `Azure` `Google Cloud` `Docker` `Linux` `NLP` `Machine Learning` `Data Science`

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 📊 GitHub Analytics

          <div align="center">

          <img
              height="165"
              src="https://github-readme-stats.vercel.app/api?username=Sohan-Rana&show_icons=true&theme=tokyonight&hide_border=true&rank_icon=github"
              alt="GitHub stats"/>

          <img
              height="165"
              src="https://github-readme-stats.vercel.app/api/top-langs/?username=Sohan-Rana&layout=compact&theme=tokyonight&hide_border=true"
              alt="Top languages"/>

          <br/>

          <img
              src="https://streak-stats.demolab.com?user=Sohan-Rana&theme=tokyonight&hide_border=true"
              alt="GitHub streak"/>

          <br/>

          <img
              src="https://github-readme-activity-graph.vercel.app/graph?username=Sohan-Rana&theme=tokyo-night&hide_border=true&area=true"
              width="100%"
              alt="GitHub activity graph"/>

          </div>

          ## 🏆 Trophies

          <div align="center">

          <img
              src="https://github-profile-trophy.vercel.app/?username=Sohan-Rana&theme=tokyonight&no-frame=true&no-bg=true&column=7&margin-w=10"
              width="100%"
              alt="GitHub trophies"/>

          </div>

          <img src="assets/divider.svg" width="100%" alt=""/>

          ## 🌱 Beyond Code

          Reading, storytelling, podcasts, and youth-led climate and community initiatives.

          I believe good technology starts with understanding people and their problems.

          ## 🤝 Let's Connect

          I'm open to internships and collaboration on research, AI / NLP / HCI, cloud, data science, climate technology and open-source projects.

          If you're building something meaningful, reach out at **sohanrana243@gmail.com**.

          <img
              src="assets/footer.svg"
              width="100%"
              alt="Thanks for visiting"/>
          '''

          with open(
              "README.md",
              "w",
              encoding="utf-8"
          ) as file:
              file.write(README)

          print("wrote README.md")

          PYEOF

      - name: Commit generated files
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"

          git add README.md assets

          if git diff --cached --quiet; then
            echo "No changes to commit."
          else
            git commit -m "Build animated profile"
            git push
          fi
