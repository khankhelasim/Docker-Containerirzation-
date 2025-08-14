# Multi-Network Container Lab

This lab demonstrates how to connect multiple containers using custom Docker networks.  
The setup includes:

- A **frontend container** (nginx)
- A **backend container** (mysql)
- An **application container** that connects to both frontend and backend

The application container acts as a bridge between the frontend and backend networks.

## Architecture

![Architecture Diagram](diagram.png)

## Learning Objectives
- Understand Docker custom networks
- Learn how containers communicate across networks
- Connect a single container to multiple networks

## Files
- `STEPS.md` – Detailed step-by-step guide to set up the lab
- `diagram.png` – Visual representation of the architecture
