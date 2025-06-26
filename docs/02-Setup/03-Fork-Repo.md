# 2.3 Fork Repo

You must create a copy (known as a fork) of the [postgres-agentic-shop](https://github.com/Azure-Samples/postgres-agentic-shop) GitHub repo and then clone that onto your local computer so you can work with its contents. After completing this step, you should have:

- [X] Forked the **postgres-agentic-shop** repo to your personal GitHub profile
- [X] Created a local clone of the repo
- [X] Opened the cloned repo in Visual Studio Code

## Fork Repo To Your Profile

Forking in GitHub refers to creating a personal copy of a public repository, which allows you to freely experiment with changes without affecting the original project.

1. To fork the repo, open a new browser window or tab and navigate to <https://github.com/Azure-Samples/postgres-agentic-shop>

2. Select the **Fork** button to create a copy of the repo in your GitHub profile.

    ![repo-fork](../img/github-fork.png)

3. Login with your GitHub profile, if prompted.

4. On the **Create a new fork** page, select **Create fork** to make a copy of the repo under your GitHub profile.

    ![create-fork](../img/create-fork.png)

5. The forked repo will open within your GitHub profile.

## Clone the Forked Repo

1. On the GitHub page for your fork, select the **Code** button and then select the **Copy URL to clipboard** button next to the repo's HTTPS clone link:

    ![copy-url](../img/copy-url.png)

2. Open a new command prompt/shell terminal and change directories to the folder in which you want to clone the repo (e.g., D:\repos).

3. Once in the desired directory, run the following `git clone` command to download a copy of your fork onto your local machine. Ensure you replace the `<url_of_your_forked_repo>` url with the clone link you copied in the previous step.

    ```bash title=""
    git clone <url_of_your_forked_repo>
    ```

4. Once the repository has been cloned, change directory at the command prompt to the folder of the cloned repo, then run the following command to open the project in Visual Studio Code:

    ```bash title=""
    code .
    ```

!!! tip "Leave Visual Studio Code open as you will be using it throughout the remainder of the workshop."