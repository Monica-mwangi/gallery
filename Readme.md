 Dark Room Project - Cloud Deployment with CI/CD

This repository contains the **Dark Room** application, which we are transforming into a cloud-based application with continuous integration and deployment using **Render**, **MongoDB Atlas**, and **Jenkins**. Follow the milestones below to complete the project.

---

## Milestone 1: Set Up

1. **Fork and Clone** this repository.  
   This is an already working application that we will be translating into a cloud-based application.

2. **Database Setup**  
   The application uses a MongoDB database. To deploy to Render, we will use **MongoDB Atlas**, a cloud-hosted MongoDB service. Follow [this tutorial](#) to set up your cluster.

3. **Configure `_config.js`**  
   Once you’ve created a cluster and a database user on MongoDB Atlas, update the `_config.js` file:  
   ```js
   const USERNAME = "<USERNAME>";
   const PASSWORD = "<PASSWORD>";

Replace <USERNAME> and <PASSWORD> with the credentials of your MongoDB Atlas user.
Milestone 2: Basic Pipeline
## settin up jenkins
Set up Jenkins Pipeline

        Create a JenkinsFile with the necessary instructions.

        The pipeline should trigger automatically whenever you push a change to your repository.

        Ensure all required software is available (npm install is useful here).

        Deploy the application to Render using node server.

    Landing Page Update

        Add a big MILESTONE 2 on the landing page.

        Push your changes and confirm that the website on Render displays MILESTONE 2.


Milestone 3: Tests

    Run Existing Tests

        Switch to the test branch to access existing tests.

        Run the tests locally:

        npm test

        Test results should resemble darkroom_test.png.

    Merge Tests

        Merge the test branch into main.

        Update your JenkinsFile to execute tests as part of the pipeline.

        Configure email notifications if tests fail.

    Landing Page Update

        Add a big MILESTONE 3 on the landing page.

        Push your changes and confirm that the website on Render displays MILESTONE 2 and MILESTONE 3.

Milestone 4: Slack Integration

    Slack Setup

        Create a Slack channel named YourFirstName_IP1.

        Invite your team members.

    Integrate Slack with Jenkins

        Update your Jenkins pipeline to send a Slack message on successful deploy.

        The message should include:

            Build ID

            Link to the Render deployment

        Use environment variables to manage sensitive information.

    Landing Page Update

        Add a big MILESTONE 4 on the landing page.

        Push your changes and confirm that the website on Render displays MILESTONE 2, MILESTONE 3, and MILESTONE 4.

Technologies Used

    Node.js

    MongoDB Atlas

    Render (Cloud Deployment)

    Jenkins (CI/CD)

    Slack (Notification Integration)