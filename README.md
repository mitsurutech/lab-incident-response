# Incident response and forensics lab: investigate a compromised Linux server

[![check](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-incident-response/actions/workflows/check.yml)

A ready-made **incident response and digital forensics lab** for classrooms, from the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning), where schools and colleges give every student their own machine and terminal in the browser. Each student gets their own Ubuntu VM holding a **compromised web server's logs and files**: an SSH log with a brute-force attack, a web server access log full of scanners, the accounts file, crontabs and a list of file modification times. They find **the attacker's IP address, the account that was broken into, the time of the first login and the persistence the intruder left behind**, then write what they found in `answers.txt`. Nothing is installed on the student's computer.

There is **no malware and no exploit code**. Everything is text, read with the tools every Linux machine has: `grep`, `awk`, `sort`, `uniq`, `cut`, `less`. The case has distractors on purpose: the address with the most failed logins never gets in, an administrator mistypes a password and then succeeds, and a legitimate user and an old SSH key sit beside the intruder's changes, so a lazy `grep` does not solve it and a careful reading of the timeline does.

## What the student does

1. Read the README the lab leaves in the home folder: it names the evidence and what to find, and gives none of the answers.
2. Work through `case/var/log/auth.log` and `case/var/log/nginx/access.log` to tell the attack from the noise and find the address and account.
3. Work out the time of the first successful login from that address.
4. Look at the crontabs, the accounts, the keys and `case/mtimes.txt` to find what the intruder left, which file it lives in and which program it keeps running.
5. Write the six labelled lines in `answers.txt`.

## How it is marked

The lab has a **check**, a script that marks a handed-in VM without the teacher reading it: it reads `answers.txt` (capital letters and spaces do not matter) and compares each answer with the case, naming the **first field that is wrong and never saying what the answer is**. It also refuses a case whose evidence files were changed to suit an answer. The case is the same for every student, made from a fixed seed when the VM first starts.

A student has `sudo` on their own VM, so one who reads root's files can find the key. That is accepted: the lab is practice, and what is marked is the written answer.

## What is in the file

`incident-response.lab.yaml` is a whole **VM lab definition**, exactly what the platform runs:

| Key | What it is |
| --- | --- |
| `cpus`, `memory`, `disk` | the size of each student's VM: 1 CPU, 1024 MB and 10 GB |
| `open` | the ports that open in the browser: none, the lab is the terminal |
| `setup` | a script run once, as root, the first time each student's VM starts: it makes the case, writes the README and an empty `answers.txt`, and installs Python from Ubuntu's archive only if the image does not have it |
| `check` | the marking script, run as root on a copy of the saved disk after a fresh boot; its exit status is the result and `0` passes |

## Using it on the platform

**To set it as work for a class:**

1. In your module, add a task and choose the **Ubuntu VM lab** card.
2. Paste the contents of `incident-response.lab.yaml`, or press the sample if your platform lists it. Using a sample replaces the text, and Undo brings yours back.
3. Publish and try your own copy first. Every student gets a VM of their own, kept between sessions.
4. Assign it like any task. When it is handed in, run the check on a student's row in the class list: it starts a copy of their saved machine that is not saved back, and shows whether it passed and what it printed.

The help manual's [page for this lab](https://mitsurutech.co.uk/help/lab-incident-response), its [guide to Ubuntu VM labs](https://mitsurutech.co.uk/help/labs) and its [sample labs page](https://mitsurutech.co.uk/help/sample-labs) cover the rest. To make your own case, change the generator in the `setup`: the seed, the addresses, the account and the files it writes.

No college account yet? [Your first year is free](https://mitsurutech.co.uk/pricing) for up to 50 students, and [signing up](https://mitsurutech.co.uk/signup) takes an email address.

## Checking the file

On every push, `.github/workflows/check.yml` checks that the file is valid YAML with the five keys above, that `setup` and `check` are each inside the platform's 16 KB limit and that the marking script is valid Python. It does not boot a VM: that is done on the platform's own machines.

## What does not work in a lab

The VM reaches Ubuntu's package archive, PyPI, npm and GitHub, and nothing else, so a lab cannot be pointed at a host on the internet. It is one VM with a log set to read: there is no live attacker to watch and no second machine to attack.

## About

Made by [Mitsuru Technologies](https://mitsurutech.co.uk) for the [MitsuruTech Learning Platform](https://mitsurutech.co.uk/learning): Python, Java, Go, Node, SQL and Linux in the browser for schools and colleges, with container labs, VM labs, web apps deployed from GitHub, and quizzes that mark themselves. Try code with no account in the [playground](https://mitsurutech.co.uk/playground).

More labs from the same place: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) and [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licence

The lab definition and this documentation are MIT licensed, see [LICENSE](LICENSE): copy them, change them, teach with them. Every address in the case is from the ranges reserved for documentation, and nothing in it is real data.
