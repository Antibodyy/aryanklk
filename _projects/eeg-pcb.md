---
title: "EEG Signal Acquisition Printed Circuit Board"
collection: projects
type: "Graduate Project"
permalink: /projects/eeg-pcb
venue: "UC Berkeley"
location: "Berkeley, CA"
order: 2
---

Designed and fabricated a custom PCB for acquiring micro-volt level EEG signals with high noise immunity.

## About This Project

This project involved developing a complete printed circuit board solution for acquiring EEG (electroencephalography) signals. The challenge required careful attention to noise isolation and signal integrity at the micro-volt level, demanding precise analog circuit design and PCB layout techniques.

## Schematics

<figure class="project-figure" style="text-align: center; margin: 0 auto 20px auto; display: block;">
    <img src="{{ '/afe_sc.png' | relative_url }}" alt="Analog Front End Schematic" style="display: block; margin: 0 auto; max-width: 100%;">
    <figcaption style="text-align: center; margin-top: 8px;"><em>Analog Front End Schematic</em></figcaption>
</figure>

<figure class="project-figure" style="text-align: center; margin: 0 auto 20px auto; display: block;">
    <img src="{{ '/p2_sc.png' | relative_url }}" alt="Secondary Schematic" style="display: block; margin: 0 auto; max-width: 100%;">
    <figcaption style="text-align: center; margin-top: 8px;"><em>System Schematic</em></figcaption>
</figure>

## My Contributions

* [cite_start]Designed and debugged a custom PCB in KiCAD for high-sensitivity analog EEG signal acquisition. [cite: 9]
* [cite_start]Implemented analog filtering and amplification stages, utilizing DFM-optimized layout to isolate micro-volt signals from ambient noise. [cite: 9]
* [cite_start]Executed hands-on board bring-up by soldering 20+ SMD components and debugging the physical signal chain to deliver a working board. [cite: 9]

## Board Layout

<div style="display: flex; justify-content: center; gap: 20px; text-align: center; margin: 20px 0;">
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/layout_fr.png' | relative_url }}" alt="Front Copper Layout" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Front Copper Layout</em></figcaption>
    </figure>
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/layout_bc.png' | relative_url }}" alt="Back Copper Layout" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Back Copper Layout</em></figcaption>
    </figure>
</div>

## Final Assembly

<div style="display: flex; justify-content: center; gap: 20px; text-align: center; margin: 20px 0;">
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/board_dig.png' | relative_url }}" alt="3D CAD Board Render" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>3D CAD Board Render</em></figcaption>
    </figure>
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/board_comp.jpeg' | relative_url }}" alt="Completed PCB Assembly" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Completed PCB Assembly</em></figcaption>
    </figure>
</div>

## Technologies & Skills

* **PCB Design**: KiCAD (schematic capture, layout, DFM optimization)
* **Circuit Design**: Analog filtering, multi-stage amplification, noise characterization
* **Prototyping**: SMD soldering, board bring-up, signal chain debugging
* **Analysis**: Signal integrity analysis, noise isolation techniques

---

**Timeline**: Aug 2025 - Dec 2025