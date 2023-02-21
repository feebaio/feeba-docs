# Account setup
1. Initially, a user needs to create an account with Feeba. 

- Prod  https://dashboard.feeba.io/auth
- Dev   https://dev-dashboard.feeba.io/auth

[<img src="images/auth.png" width="250"/>](images/auth.png)

    > ℹ️ A user may add more projects after creating an account.


2. After creating an account, you will be redirected main page of the dashboard.


[<img src="images/sign-in.png" width="900"/>](images/sign-in.png)


3. In order to get rates from different platform, a user needs to add configurations.


# Configurations
You can click on Global settings button, which will redirect you to configuration page. This page is important because you will need to add all token infformation from third-party apps like Trello or Telegram. Currently, only Trello and Telegram supported and new apps will be integrated in the future.

[<img src="images/config.png" width="900"/>](images/config.png)


## Global settings
- Trello - section where a user enters all necessary trello private configurations
- Telegram - similar to trello, a section for configuration. Keep in mind you'll need to creat a bot and add it in the channel to receive all updates throug dashboard.
- SDK Token - you need to generate token and use that token in the integrated apps and platforms. See Android and iOS sections how to use this token.

## Future integrations
- Slack
- Jira
