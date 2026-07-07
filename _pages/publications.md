---
layout: page
permalink: /publications/
title: publications
nav: true
nav_order: 2.1
---

<div class="pub-controls" style="margin-bottom:1rem;">
  <div style="margin-bottom:0.5rem;">
    <strong>Filter:</strong>
    <button class="filter-btn active" data-filter="all">All</button>
    <button class="filter-btn" data-filter="selected">Selected</button>
  </div>
  <div>
    <strong>Area:</strong>
    <span id="area-buttons" class="area-buttons"></span>
  </div>
</div>

<div id="pub-list">
{% bibliography --group_by year %}
</div>

<link rel="stylesheet" href="/assets/css/publications-filter.css">

<script>
document.addEventListener('DOMContentLoaded', function(){
  const entries = Array.from(document.querySelectorAll('.pub-entry'));
  const areaContainer = document.getElementById('area-buttons');
  const categories = new Set();
  entries.forEach(function(e){
    const c = e.dataset.category;
    if(c){
      c.split(/[,;]+/).map(function(s){ return s.trim(); }).filter(Boolean).forEach(function(cat){ categories.add(cat); });
    }
  });
  // create area buttons
  const allBtn = document.createElement('button'); allBtn.className='area-btn active'; allBtn.textContent='All'; allBtn.dataset.area='all'; areaContainer.appendChild(allBtn);
  categories.forEach(cat=>{ const b=document.createElement('button'); b.className='area-btn'; b.textContent=cat; b.dataset.area=cat; areaContainer.appendChild(b); });

  function applyFilters(){
    const selectedFilter = document.querySelector('.filter-btn.active')?.dataset.filter || 'all';
    const activeArea = document.querySelector('.area-btn.active')?.dataset.area || 'all';
    entries.forEach(function(e){
      let show=true;
      if(selectedFilter==='selected'){
        show = (e.dataset.selected && e.dataset.selected != 'false');
      }
      if(show && activeArea!=='all'){
        const catStr = (e.dataset.category||'');
        const entryCats = catStr.split(/[,;]+/).map(function(s){ return s.trim().toLowerCase(); }).filter(Boolean);
        const kws = (e.dataset.keywords||'').toLowerCase();
        show = (entryCats.indexOf(activeArea.toLowerCase()) !== -1) || (kws.indexOf(activeArea.toLowerCase())!==-1);
      }
      e.style.display = show ? 'flex' : 'none';
    });
  }

  // filter button handlers
  document.querySelectorAll('.filter-btn').forEach(b=> b.addEventListener('click', function(){ document.querySelectorAll('.filter-btn').forEach(x=>x.classList.remove('active')); this.classList.add('active'); applyFilters(); }));
  document.querySelectorAll('.area-btn').forEach(b=> b.addEventListener('click', function(){ document.querySelectorAll('.area-btn').forEach(x=>x.classList.remove('active')); this.classList.add('active'); applyFilters(); }));
  // also support area buttons created dynamically
  areaContainer.addEventListener('click', function(ev){ if(ev.target && ev.target.matches('.area-btn')){ document.querySelectorAll('.area-btn').forEach(x=>x.classList.remove('active')); ev.target.classList.add('active'); applyFilters(); }});

  applyFilters();
});
</script>
