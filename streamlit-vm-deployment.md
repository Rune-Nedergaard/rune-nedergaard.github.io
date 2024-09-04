---
title: "Development notes on safely deploying a streamlit app on VM"
layout: default
---


install docker: https://docs.docker.com/engine/install/ubuntu/
note not compatible with ufw


man skal bruge ngnix til at redirecte fra port 8501 e.g. (over 1024) til port 80, fordi kræver ellers admin. Muligt at køre med admin #sudo -E /home/RUN/miniconda3/envs/mask/bin/python -m streamlit run app.py --server.port 80 --server.address 0.0.0.0
men ikke recommended - også docker problemer. 

