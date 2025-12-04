# Lab 1 - Build

### Summary: Setup a CI Pipeline, including running source code tests, building the executable, building and pushing the artifact to a remote repository

### Outcome: A Deployable artifact

### Learning Objective(s):

- Configure a basic pipeline using Harness CIE

- Build and Deploy an artifact to a remote repository using Harness CIE

- Run unit tests during the process to verify that the build is successful using Harness CIE

**Steps**

1. From the left hand menu, navigate to **Projects** → **Select the project available**\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXfhuMykMsIHl-7FjliWssHc0uwRpdLdrnq7GkGAI0g6UBZM69F1zpQ8ZA8N_vMqjpoGFYFR_weJk7OtOGGa2bksIaS6BlktwytmuJ1THM3e8O6tDT18HYWwFyGUye8ubsrHBChI8ORrCQ88JcKWpLjQ0DsXDS0NSZrkfZ4RUQ?key=cRG2cvp_PHVW0KG2Gq6Y_A)

1) From the left hand side menu select **Pipelines**

2) Click **+ Create a Pipeline**, enter the following values, then click **Start**

| Field                                  | Value            | Notes
| -------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| Name                                   |workshop|                                                                                            |
| How do you want to setup your pipeline |Inline| This indicates that Harness (rather than Git) will be the source of truth for the pipeline |

3. From Pipeline Studio, Click **Add Stage** and select **Build** as the Stage Type

4. Enter the following values and click on **Set Up Stage**



| Input           | Value           | Notes |
| --------------- | --------------- | ----- |
| Stage Name      |Build|       |
| Clone Codebase  |Enabled|       |
| Repository Name |harnessrepo|       |

5. There are two main tabs that need configuration:\
   **Infrastructure**



| Input          | Value | Notes |
| -------------- | ----- | ----- |
| Infrastructure |Cloud|       |

**Execution**

- Select **Add Step**, then **Add Step** again, then select **Test Intelligence** from the Step Library and configure with the following



| Input                        | Value                                      | Notes                                                                                                                                             |
| ---------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                         |Run Tests With Intelligence|                                                                                                                                                   |                                   |
| Command                  |pip install pytest & cd ./python-tests| The github repo is a monorepo with application(s) and configuration in the same repo. Therefore we need to navigate to the application subfolder. |                                                                                                                 |


- After completing configuration select **Apply Changes** from the top right of the configuration popup

- Select **Add Step**, then **Use template** (In this step we will be building the binary following same config as before. To avoid duplication of efforts a template has been precreated)



| Input         | Value               | Notes |
| ------------- | ------------------- | ----- |
| Template Name |Compile Application|       |

- Select the  template and press **Use Template,** then provide a name for that template



| Input | Value   | Notes |
| ----- | ------- | ----- |
| Name  |Compile|       |

- Select **Add Step**, then **Add Step** again, then select **Build and Push an image to Docker Registry** from the Step Library and configure with the following



| Input             | Value                                               | Notes                                                                    |
| ----------------- | --------------------------------------------------- | ------------------------------------------------------------------------ |
| Name              |Push to DockerHub|
| Registry Type     |Third-Party Artifact Registry|
| Docker Connector  |att-dockerhub|                                                                          |
| Docker Repository |cassiesouza/workshop|                                                                          |
| Tags              |<+variable.username>-<+pipeline.sequenceId>| This will be the tag of the image using harness expressions              |
| Dockerfile        |/harness/frontend-app/harness-webapp/Dockerfile| This tells harness where is the Dockerfile for building the app          |
| Context           |/harness/frontend-app/harness-webapp| This tells from where to run the instructions included in the dockerfile |

1. Click **Apply Changes** to close the config dialog

6) Click **Save** and then click **Run** to execute the pipeline with the following inputs


| Input       | Value | Notes        |
| ----------- | ----- | ------------ |
| Branch Name |main| prepopulated |


# Lab 2 - DevSecOps

**Summary:** Our security team has implemented orchestration of **Fortify** and **OWASP** scans for our code in a reusable form **(templates)**. In order to improve our security posture they have also added policies to enforce us to include those templates

![](https://lh7-us.googleusercontent.com/docsz/AD_4nXcLr5TGcKRWOjVgB_sCAHHEeLPyd6EBdnkt2-mq_imTkZbQMEwJD03Q1wZyhWqHxoCNIIYWJWlRbnZrvZn2pPYIwTzXlOGdhMDEgn-J2JnK7lVastmfpdwTqDHXjpP0DK3TgU1gM-Ec_0iZLicWV7KpgW2FdXUCcAtraDGaEz8hI3dpWGLXkg?key=cRG2cvp_PHVW0KG2Gq6Y_A)

**Learning Objective(s):**

- Understand how governance plays a role in the path to production

- Reusable templates make developer’s life easier

- DevSecOps practices can be easily achieved

**Steps**

1. In the existing pipeline, within the Build stage **before** PushToDockerhub step click on the plus icon to add a new step

2. Select use template\
   ![](https://lh7-us.googleusercontent.com/docsz/AD_4nXeC5rTVxlk7DeZeU_cINwcKo6Nf2wVW9brQ9MiCEfppJwmU-uH3QcNZ53qTxhur57KeySksoDBg9EqjhgKOgAEDKon6iNz9cFxozBe9VZssV-t77VNo6t1zPUvm6e2NOZJDKncxd9c2GM4HE-h-L4cIOl4u6Uqx_azoKchMdg?key=cRG2cvp_PHVW0KG2Gq6Y_A)

3. Select **DevX Fortify Scan** 

4. Name the step **Fortify**

5. In the existing pipeline, within the Build stage **after** PushToDockerhub step click on the plus icon to add a new step

6. Select use template

7. Select **OWASP**

8. Name the step OWASP

9. Click **Save** and then click **Run** to execute the pipeline with the following inputs



| Input       | Value | Notes |
| ----------- | ----- | ----- |
| Branch Name |main|       |

After the **Build and Push** stage is complete, go to the **Security Tests** tab to see the deduplicated, normalized and prioritized list of vulnerabilities discovered across your scanners.


# Lab 3 - Configure Gitops Application

### Summary: Configure a GitOps Application by setting up a GitOps Repository with credentials, creating a GitOps Application with proper configuration, and connecting to a GitOps Agent to enable continuous deployment using Argo CD

**Learning Objective(s):**

- Create a k8s gitops service
- Configure a GitOps Repository with credentials
- Create a GitOps Application with proper configuration
- Connect to a GitOps Agent at the organization level

**Steps**

1. Add a GitOps Repository

- In your Harness project, expand Deployments, select GitOps, and then Settings
- Select Repositories
- Select New Repository
- In Specify Repository Type, select Git
- Enter the following 


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Repository Name | workshop                                         |       |
| GitOps Agent    | att-workshop                                |  Click on Organization to find the correct agent      |
| Git Repository URL | <Add YOUR lab repo url from Github>  |     |

- Click on Continue
- Once at Credentials, select Specify Credentials For Repository, then add the following input
   - Connection Type: **HTTPS**
   - Authentication to **Anonymous**
- Click on Save & Continue
- Select Finish

2. Add a GitOps Application Set

- In the context of Gitops, select Applications in the top right hand corner
- Click on Application Set
- Click on **+New Application Set**
   - App Set Name: *frontend-appset-<input unique username>*
   - GitOps Agent: *att-workshop*
   - Click **+New Service** and configure as follows:


| Input                      | Value                                               | Notes                              |
| -------------------------- | --------------------------------------------------- | ---------------------------------- |
| Name                       |frontend|                                    |
| Deployment Type            |Kubernetes|                                    |
| * **Add Release Repo Manifest**         |                                                     |                                    |                                   |
| Release Repo Store         |Github|                                    |
| Connector        |ATT Workshop|                                    |
| Manifest Identifier        |clusterconfig|                                    |
| Repository                 |workshop-souzatt6243120|                                    |
| Branch                     |main|                                    |
| File Path           |gitops-workshop/cluster-config/<+env.name>/config.json|                                    |
| * **Add Deployment Repo Manifest**         |                                                     |                                    |                                   |
| Deployment Repo Store         |Github|                                    |
| Connector        |ATT Workshop|                                    |
| Manifest Identifier        |appset|                                    |
| Repository                 |workshop-souzatt6243120|                                    |
| Branch                     |main|                                    |
| File Path           |gitops-workshop/appset.yaml|                                    |
| **Add Artifact Source**          |                                                      |                                                                          |
| Artifact Repository Type         | Docker Registry                                      |                                                                          |
| Docker Registry Connector        | att-dockerhub                                        |                                                                          |
| Artifact Source Identifier       | workshop                                             |                                                                          |
| Image Path                       | cassiesouza/workshop                                 |                                                                          |
| Tag                              | <+variable.username>-<+pipeline.sequenceId>          | Click on the Sigma next to the input box and choose Expression Variable  |

- Click on Save

- In Environment, select **+New Environment**, name the Environment dev, and select Pre-Production
- Select Save, then Continue
- In Sync Policy, select Create-Update, and then select Continue
- In Generators, select Continue
- In Preview, navigate to your repository at the path *gitops/workshop/manifests/appset.yaml*. Copy the entire contents, and paste them into the Preview
- UPDATE FILE 
   - Find *<add unique username>* and replace with your unique username
- Then *Validate*
- Once validated, select Finish

** Your appset should begin cascading two applications. One for dev and one for prod. This will take a couple of minutes**

# Lab 4 - Create PR Pipeline - Dev

### Summary: Extend your existing pipeline to take the artifact built in the CI/Build stage and deploy it to a dev environment using GitOps

**Learning Objective(s):**

- Add a Deploy stage to an existing pipeline

- Create PR pipeline flow for a dev environment

1. In your existing pipeline, add a Deployment stage by clicking **Add Stage** and select **Deploy** as the Stage Type

2. Enter the following values and click on **Set Up Stage**


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |Deploy to Dev|       |
| Deployment Type |Kubernetes|       |
| GitOps |Enabled|       |

**Service**

- Click **- Select -** on the service input box ****

- Select **frontend** service****

**Environment**

The target infrastructure has been pre-created for us. The application will be deployed to a k8s cluster on the given namespace  

- Click **- Select -** on the environment input box

- Select **dev** environment

| Input | Value | Notes                                                             |
| ----- | ----- | ----------------------------------------------------------------- |
| Name  |dev| Make sure to select the environment and gitops clusters |

- Click the radio button *Deploy to all clusters*

**Execution**

- There should be three steps which are autopopulated: Update Release Repo, Merge PR, and Fetch Linked Apps

**Update Release Repo Step**
- Click on the Update Release Repo step, open Optional Configuration and input the following values under **Variables**:


| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| image_tag       |<+artifacts.primary.tag>|       |
| harness_service |<+service.identifier>|       |
| environment     |<+environment.identifier>|       |
| username     |<+pipeline.variables.username>|       |

**Add PR Approval Step**
- **After** the Update Release Repo and **before** the Merge PR add **Harness Approval** step according to the table  below

- Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |PR Approval|       |
| User Groups |All Project Users|       |

**Modify Fetch Linked Apps**
- In the Fetch Linked Apps step, enable the **Filter applications per configured service/env** radio button
- Apply Changes

**Add GitOps Sync Step**
- After Fetch Linked Apps, **+Add Step**, select **GitOps Sync** and add the following values

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |GitOps Sync|              |

- Click on Apply Changes
- Click on Save

**Add Username Pipeline Variable**
- Go to the right nav bar, click on Variables
- Click **+ Add Variable** in the first section
- Set Name to `username`
- Set Value to `<A unique username>`
- Set variable as required during runtime
- Click **Apply Changes**
- Click **Save**

**Add GitOps Cluster to Dev Environment**

- Expand Deployments, then go to Environments, click on dev
- Once in the dev environment, go to GitOps Clusters, click **+ Select Cluster(s)**
- Choose Organization at the top, then the in-cluster agent for for the NON prod Agent Id (e.g Agent Id: org.attworkshop)
- Click Apply Selected

**Run Pipeline**
- Navigate to Pipelines
- Click on the workshop pipeline
- Click on Run

# Lab 5 - Continuous Deployment - Production

### Summary: Extend your existing pipeline to deploy to a production environment using GitOps with an advanced deployment strategy

### Learning Objective(s):

- Deploy to a production environment using GitOps

- Incorporate an advanced deployment strategy such as Canary

1. In the existing pipeline, add a Deployment stage by clicking **Add Stage**, select **Use template**, and select **Argo Rollouts - Prod Promotion**, then **Use template**

2. About your Stage


| Input           | Value          | Notes |
| --------------- | -------------- | ----- |
| Stage Name      |Deploy Prod|       |

3. Click on Setup Stage

4. Under **Template Inputs** 

- Configure the **Template Inputs** with the following settings:
  - For **Service**, select "Propogate from: [Deploy] - Service [frontend]"
  - Click **Deploy to Different Environment**
  - In **Specify Environment**, click on the sigma icon on the right, choose Fixed Value, and select **prod** from the dropdown
  - Click Apply Selected

6. Click **Save**
7. Click **Run** Pipeline

# Lab 6 - Governance/Policy as Code

### Summary: Create and apply policies as code in order to enable governance and promote self-service. In Lab 2 we saw how a user is impacted by policies in place, now is the time to create such policies

**Learning Objective(s):**

- Create a policy that evaluates when editing pipelines

- Create a policy that evaluates during pipeline execution

- Test policy enforcement

**Steps**
**Create a Policy to require Approvals**

1. From the secondary menu, select **Project Settings** and select **Governance Policies**

2. Click **Build a Sample Policy**

3. From the suggested list select **Pipeline - Approval**  and click on next

4. Click Next: Enforce Policy

5. Set the values according to the table  below and confirm

| Input            | Value        | Notes |
| ---------------- | ------------ | ----- |
| Trigger Event    |On Run|       |
| Failure Strategy |Error & exit|       |

**Test the Policy to require Approvals**

1. Open your pipeline

2. Try to run the pipeline and note that the failure due to lack of an approval stage

3. Open the pipeline in edit mode and **Add Stage**

4. Select Approval Stage


| Input            | Value            | Notes |
| ---------------- | ---------------- | ----- |
| Stage Name        |Prod Approval|       |
| Type of Approval |Harness Approval|       |

5. Configure the Approval step as follows

| Input       | Value             | Notes |
| ----------- | ----------------- | ----- |
| Name        |Approval|       |
| User Groups |All Project Users|       |

6. Click **Save** and note that the save succeeds without any policy failure