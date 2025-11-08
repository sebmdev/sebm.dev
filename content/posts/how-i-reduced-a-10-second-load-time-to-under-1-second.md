+++
date = '2025-11-08T19:59:38+08:00'
draft = false
title = "How I Reduced a 10-Second Load Time to Under 1 Second"
subtitle = "Optimizing an Employee Directory for Speed, Accessibility, and Empathy"
author = "Sebastian Martinez"
tags = ["optimization", "performance", "case-study"]
categories = ["backend"]
+++

{{< admonition type=info title="Important notice" open=true >}}
This is a draft for testing the blog. Will actually fill in the details at a later date.
{{< /admonition >}}

{{< mermaid >}}
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop Healthcheck
        John->John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail...
    John-->Alice: Great!
    John->Bob: How about you?
    Bob-->John: Jolly good!
{{< /mermaid >}}

Inline Formula: {{< raw >}}\(\mathbf{E}=\sum_{i} \mathbf{E}_{i}=\mathbf{E}_{1}+\mathbf{E}_{2}+\mathbf{E}_{3}+\cdots\){{< /raw >}}

Block Formula:

{{< raw >}}
\[ a=b+c \\ d+e=f \]
{{< /raw >}}

Raw content using Markdown syntax: {{< raw >}}**Hello**{{< /raw >}}

I messaged {{< person url="https://sebm.dev" name="Sebastian Martinez" text="author of this blog" >}} for ideas about...

## 🧩 Background

While working on a feature that involved loading our company’s internal **Employee Directory**, I noticed something frustrating:  
every refresh took about **10 seconds** — and that was on a fast connection.

During debugging, I realized it wasn’t just my local machine struggling.  
The API was returning roughly **5 MB of data** per request, and every load pulled **every single employee record** with redundant nested information.

For users accessing the system from the **province**, where connectivity can be limited and data costs are higher, this wasn’t just inconvenient — it was **unfairly inaccessible**.

So I decided to do something about it.

---

## 🧠 The Root Cause

After tracing the calls, I found that the backend endpoint was:

- Fetching **all employee data**, including non-essential relational fields.  
- Returning large nested JSON structures.  
- Performing **no pagination or projection**, which meant unnecessary network load.  
- Forcing the frontend to render huge payloads before showing anything.

In short, it was a case of *over-fetching meets under-optimizing*.

---

## 🧰 The Approach

I filed a ticket for this improvement, explaining the rationale:

> “This optimization aims to improve accessibility for users with limited network bandwidth and reduce data usage for those outside the capital region.”

With approval, I reworked the API and the corresponding frontend layer.

### Backend Changes
- Introduced **field projection** to only return the necessary fields.  
- Added **server-side pagination** to handle results in smaller batches.  
- Optimized serialization to eliminate redundant nested data.  
- Added query-level caching for repeated list requests.

### Frontend Changes
- Updated the data model to handle paginated results.  
- Implemented lazy loading and smoother transitions for better UX.  
- Reduced unnecessary re-renders by improving state management.

---

## ⚡ The Result

After deploying the change and running benchmarks:

| Metric | Before | After |
|--------|--------|--------|
| Load Time | ~10 seconds | **0.17 seconds** |
| Response Size | ~5 MB | **300 KB** |
| Improvement | — | **98% faster**, **94% smaller payload** |

{{< echarts >}}

{
  "title": {
    "text": "Employee Directory Load Time Optimization",
    "left": "center"
  },
  "tooltip": {
    "trigger": "axis",
    "axisPointer": { "type": "shadow" },
    "formatter": "{b0}: {c0}s"
  },
  "xAxis": {
    "type": "category",
    "data": ["Before Optimization", "After Optimization"]
  },
  "yAxis": {
    "type": "value",
    "name": "Load Time (seconds)",
    "min": 0
  },
  "series": [
    {
      "name": "Load Time (s)",
      "type": "bar",
      "data": [
        {
          "value": 10,
          "itemStyle": { "color": "#e57373" }
        },
        {
          "value": 0.17,
          "itemStyle": { "color": "#81c784" }
        }
      ],
      "label": {
        "show": true,
        "position": "top",
        "formatter": "{c}s"
      },
      "barWidth": "40%"
    }
  ]
}


{{< /echarts >}}

{{< echarts >}}
{
  "title": {
    "text": "Employee Directory Response Size Reduction",
    "left": "center"
  },
  "tooltip": {
    "trigger": "axis",
    "axisPointer": { "type": "shadow" },
    "formatter": "{b0}: {c0} KB"
  },
  "xAxis": {
    "type": "category",
    "data": ["Before Optimization", "After Optimization"]
  },
  "yAxis": {
    "type": "value",
    "name": "Response Size (KB)",
    "min": 0
  },
  "series": [
    {
      "name": "Response Size (KB)",
      "type": "bar",
      "data": [
        {
          "value": 5000,
          "itemStyle": { "color": "#64b5f6" }
        },
        {
          "value": 300,
          "itemStyle": { "color": "#4db6ac" }
        }
      ],
      "label": {
        "show": true,
        "position": "top",
        "formatter": "{c} KB"
      },
      "barWidth": "40%"
    }
  ]
}

{{< /echarts >}}


Even on a **mobile hotspot**, the directory now loads almost instantly.

---

## 💬 Reflection

This optimization started out of personal frustration while debugging, but it turned into something bigger — a reminder that **performance is part of accessibility**.

Not everyone using your app has fiber internet or unlimited data.  
Sometimes, shaving off a few megabytes isn’t just a technical win — it’s a way to make software more inclusive.