# Clone a github project 

On most of the clusters, the authentification of your github account can be done using a ssh key. All the informations to generate a key, add it to the agent, and couple it to your github account can be found [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

Some legacy systems doesn't support the Ed25519 algorithm. An alternative to clone the project is to use HTTPS. For that, you will need to create a Personal Access Token in the github API. The documentation can be found [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). Once you have set your token, you can clone your project using: 
```bash
git clone https://<username>:<personal access token>@github.com/<your organisation or username>/<your repo>.git
```
