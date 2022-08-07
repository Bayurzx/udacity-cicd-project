[![Bayurzx](https://circleci.com/gh/Bayurzx/udacity-cicd-project.svg?style=svg)](https://app.circleci.com/pipelines/github/Bayurzx/udacity-cicd-project)

# Give your Application Auto-Deploy Superpowers

In this project, I was able to prove my understanding of DevOps CI/CD with the following objectives:

- Explaining to management the fundamentals and benefits of CI/CD with a powerpoint presentation in a language they would unnderstand. Benefits such as achieving safe integration, building a continuous pipeline for production, and deployment automation for cloud-based software products.
- Utilize Deployment Strategies to design and build CI/CD pipelines that support Continuous Delivery processes.
- Utilize a configuration management tool to accomplish deployment to cloud-based servers.
- Surface critical server errors for diagnosis using centralized structured logging.

### I was able to
- Automate creation of backend and frontend infrastructure with the help of `Cloudformation`
- Configure infrastructure with configuration management tools
- **Run tests** easily, with scripts in the application to test out the code
- **Analyse and scan** the modules and libraries used `(npm)`
- **Setup alerts** through slack channels notification to quickly inform us of a pass or fail event during deployment automation
- **Create database** for our backend in AWS
- **Setup CLoudFront Distribution** for our frontend and keep our files in s3
- Imitate **bluegreen deployments** with our frontend through cloudformation
- Automatically run **database migrations** 
- Perform **smoke test** before pushing to production
- Perform **rollback** to destroy environments and revert migrations in the case of smoke-test failures, infrastructure deployment failures
- Perform **cleanup** of any infrastructure not in use.

### Instructions

* [Selling CI/CD](instructions/0-selling-cicd.md)
* [Getting Started](instructions/1-getting-started.md)
* [Deploying Working, Trustworthy Software](instructions/2-deploying-trustworthy-code.md)
* [Configuration Management](instructions/3-configuration-management.md)
* [Turn Errors into Sirens](instructions/4-turn-errors-into-sirens.md)


### Built With

- [Circle CI](www.circleci.com) - Cloud-based CI/CD service
- [Amazon AWS](https://aws.amazon.com/) - Cloud services
- [AWS CLI](https://aws.amazon.com/cli/) - Command-line tool for AWS
- [CloudFormation](https://aws.amazon.com/cloudformation/) - Infrastrcuture as code
- [Ansible](https://www.ansible.com/) - Configuration management tool
- [Prometheus](https://prometheus.io/) - Monitoring tool

## Some Pics
![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT04_1.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT06.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT07.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT11_1.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT10_1.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/SCREENSHOT12_new.jpg)

---

![alt](https://raw.githubusercontent.com/Bayurzx/udacity-cicd-project/master/SCREENSHOTS%20(mine)/URL05_targets.jpg)

---


### License

[License](LICENSE.md)

