---
layout: post
title: "Ansible: delegate_to and variables"
description: ""
category: Configuration Management
tags: [ansible]
redirect_from:
  - /configuration management/2015/07/23/ansible-delegate_to-and-variables/
---
One nice feature of Ansible is its ability to "delegate" tasks to the control machine, or any other host that the control machine can SSH to.

The official documentation for the usage of **delegate_to** is [here](http://docs.ansible.com/ansible/playbooks_delegation.html)

## Waiting for a server to restart using delegate_to

I was playing around with a task that could delegate a VM to attempt to test port 22 on a VM that isn't connected directly to the internet, and I couldn't figure out why this didn't work;

```
{% raw %}
- name: Wait for the server to come back
  wait_for:
    host: "{{ inventory_hostname }}"
    port: 22 
    state: started
  sudo: false
  delegate_to: "{{ ssh_delegate }}"
{% endraw %}  
```

but this worked as expected;

```
{% raw %}
- name: Wait for the server to come back
  wait_for:
    host: "{{ inventory_hostname }}"
    port: 22 
    state: started
  sudo: false
  delegate_to: 10.128.0.100
{% endraw %}  
```

This was quite irritating, as I wanted to be able to define the hostname in a variable taken from [Terraform](https://www.terraform.io)

## The Fix

For some reason, putting the **delegate_to** hostname in a with_items loop works without any issues.

```
{% raw %}
- name: Wait for the server to come back
  wait_for:
    host: "{{ inventory_hostname }}"
    port: 22 
    state: started
  sudo: false
  delegate_to: "{{ item }}"
  with_items:
    - "{{ ssh_delegate }}"
{% endraw %}  
```

I have raised a [github issue](https://github.com/ansible/ansible/issues/11705) for this.