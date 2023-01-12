# 100 - Create a Hello World Github Action

Based on "Hello GitHub Actions" at https://github.com/skills/hello-github-actions

## Activity: Create a workflow file

1. Navigate to the **Code** tab (for easy, use a duplicate tab in your browser in which you will execute these instructions).
2. From the **main** branch dropdown, click on the ```welcome-workflow``` branch (if not existent, create such a branch).
3. Navigate to the ```.github/workflows/``` folder, then select **Add file** and click on **Create new file**.
4. In the **Name your file...** field, enter ```welcome.yml```.
5. Add the following content to the welcome.yml file:

```
name: Post welcome comment
on:
  pull_request:
    types: [opened]
```    
    
7. To commit your changes, click **Commit new file**.
8. Wait about 20 seconds for actions to run, then refresh this page (the one you're following instructions from) and an action will automatically close this step and open the next one.
