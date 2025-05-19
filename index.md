---
title: Globetrotter Documentation
layout: home
nav_order: 10
permalink: /
---

<div class="navigation-grid">
  <a href="/fusion/" class="nav-card">
    <div class="icon">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="40" height="40" fill="none" stroke="#a2b9fa" stroke-width="2">
        <path d="M13 2L3 14h9l-1 8 10-12h-9l1-8z"/>
      </svg>
    </div>
    <div class="content">
      <h3>GLOBETROTTER FUSION</h3>
      <p>Globetrotter's client-facing REST API platform.</p>
    </div>
  </a>
</div>

<style>
  .navigation-grid {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    margin: 20px 0;
  }
  
  .nav-card {
    display: flex;
    align-items: center;
    background-color: #1e2b3c;
    border-radius: 4px;
    padding: 20px;
    text-decoration: none;
    width: 100%;
    max-width: 500px;
    transition: background-color 0.2s;
  }
  
  .nav-card:hover {
    background-color: #2a3b4c;
    text-decoration: none;
  }
  
  .icon {
    margin-right: 20px;
    background-color: #182536;
    padding: 15px;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  .content h3 {
    color: #a2b9fa;
    margin: 0 0 5px 0;
    font-size: 14px;
    letter-spacing: 1px;
  }
  
  .content p {
    color: #a2c4f0;
    margin: 0;
    font-size: 16px;
  }
  
  @media (max-width: 768px) {
    .navigation-grid {
      flex-direction: column;
    }
  }
</style>