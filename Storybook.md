# STORYBOOK

## What is storybook

![Storybook](https://raw.githubusercontent.com/storybooks/storybook/master/media/storybook-intro.gif)

- It's an environment to dev and test UI easily
- It helps you build UI components isolated from the business logic and context of your app
- It's run on independence environment with another port with app
- With storybook, you can easily develop specific component for app with `CDD` (Component driven development) methodology, from smallest component to screen/page UI. And you can show the component UI on storybook board as the demo for your team and to discuss/change/get feedback from designer in the early phase of development process.

- More: `Component-Driven Development` allows you to gradually expand complexity as you move up the component hierarchy. Among the benefits are a more focused development process and increased coverage of all possible UI permutations. In short, CDD helps you build higher-quality and more complex user interfaces.

## How to apply to front-end project

### Install dependencies

- Setup React Storybook: https://storybook.js.org/

  > npx create-react-app taskbox

  > cd taskbox

  > npx -p @storybook/cli sb init

### Start storybook

Run the test runner (Jest) in a terminal:

> yarn test --watchAll

Start the component explorer on port 9009:

> yarn storybook

Run the frontend app proper on port 3000:

> yarn start

### Create stories

You create a React component as normally (`src/components/Task.js`). Then create a stories file for that component (`src/components/Task.stories.js`). Stories file contain the use cases of this component, e.x: in loading status, in empty list, in error, in default case...

There are two basic levels of organization in Storybook: the component and its child stories. Think of each story as a permutation of a component. You can have as many stories per component as you need.

- Component
  - Story
  - Story
  - Story

#### How to write stories for container component (connect Redux)

- If container component just contain dummy components, then just export function component beside export default connect redux
- If container component contain other container components, we need to supply a `Redux store` to this container stories file.

```javascript
import React from "react";
import { action } from "@storybook/addon-actions";
import { Provider } from "react-redux";

import { PureInboxScreen } from "./InboxScreen";
import { defaultTasksData } from "./TaskList.stories";

export default {
  component: PureInboxScreen,
  title: "InboxScreen",
  decorators: [(story) => <Provider store={store}>{story()}</Provider>],
};

// A super-simple mock of a redux store
const store = {
  getState: () => {
    return {
      tasks: defaultTasksData,
    };
  },
  subscribe: () => 0,
  dispatch: action("dispatch"),
};

export const Default = () => <PureInboxScreen />;

export const Error = () => <PureInboxScreen error="Something" />;
```

Above example is `PureInboxScreen` container that include `TaskList` container, both connect to Redux store. We create a mock store to provide for them

### Deploy Storybook

Throughout this tutorial, we built components on our local development machine. At some point, we'll need to share our work to get team feedback. Let's deploy Storybook online to help teammates review UI implementation.

To deploy Storybook, we first need to export it as a static web app. Then publish it to static host or using [Chromatic](https://www.chromatic.com/) - a free publishing service made by the Storybook maintainers. It allows us to deploy and host our Storybook safely and securely in the cloud.

#### Exporting as a static app

Running yarn build-storybook will output a static Storybook in the `storybook-static` directory, which can then be deployed to any static site hosting service

> yarn build-storybook

#### Publish Storybook

Install chromatic

> yarn add -D chromatic

Setup a repository in GitHub to hold project code

Link github repo with chromatic service

Copy the unique `project-token` that was generated for your project. Then execute it, by issuing the following in the command line, to build and deploy our Storybook

> npx chromatic --project-token=project-token

Notice that replace `project-token` in command line with your project token

When finished, you'll get a link https://random-uuid.chromatic.com to your published Storybook. Share the link with your team to get feedback.

#### CI and CD with storybook

##### Add a GitHub Action to deploy Storybook

In the root folder of our project, create a new directory called `.github` then create another workflows directory inside of it.

Create a new file called `chromatic.yml` like the one below. Replace to change project-token with your project token.

```yml
# .github/workflows/chromatic.yml
# name of our action
name: "Chromatic Deployment"
# the event that will trigger the action
on: push

# what the action will do
jobs:
  test:
    # the operating system it will run on
    runs-on: ubuntu-latest
    # the list of steps that the action will go through
    steps:
      - uses: actions/checkout@v1
      - run: yarn
      - uses: chromaui/action@v1
        # options required to the GitHub chromatic action
        with:
          # our project token, to see how to obtain it
          # refer to https://www.learnstorybook.com/intro-to-storybook/react/en/deploy/
          projectToken: project-token
          token: ${{ secrets.GITHUB_TOKEN }}
```

##### Push code and chromatic run CI/CD for you

Once you’ve set up the GitHub action. Your Storybook will be deployed to Chromatic whenever you push code. You can find all the published Storybook’s on your project’s build screen in Chromatic.

![chromatic build](https://www.learnstorybook.com/intro-to-storybook/chromatic-user-dashboard.png)

Click to a build process to view latest deploy storybook for this build. Use the link and share it with your team members. This is helpful as a part of the standard app development process or simply to show off work

#### Visual testing for Storybook

Storybook is a fantastic tool for visual regression testing because every story is essentially a test specification. Each time we write or update a story we get a spec for free!

Visual regression testing relies on comparing images of the new rendered UI code to the baseline images. If a UI change is caught we'll get notified.

After you change code and push to github repo, you make PR and Chromatic CI service will run to test your PR.

You can click to build process to review the changes. In this step, you can assign other reviewers or you can review by yourself. The review process through all change step and need mark approved on each step.

Note that you need run `npx chromatic --project-token=project-token` for Chromatic run build and deploy latest version of code.

#### Addons with storybooks

Storybook boasts a robust system of [addons](https://storybook.js.org/docs/addons/introduction/) with which you can enhance the developer experience for everybody in your team.

You can [create your own addons](https://www.learnstorybook.com/intro-to-storybook/react/en/creating-addons/) that will super charge your development

## What is pros and cons of storybook

| Props                                                                                         | Cons                                                                                                                                      |
| --------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Storybook is a powerful tool for develop UI component                                         | Need time to learn and understand the meaning of Storybook                                                                                |
| It allows you to develop UI components rapidly without worrying about the app                 | Sync code repository with chromatic to get full functionality of Storybook (visual test, CI/CD with UI,...) Github is ok, but Azure repo? |
| It will improve your team’s collaboration and feedback loop                                   |                                                                                                                                           |
| React Storybook is an open source tool with 51.2K GitHub stars and 5K GitHub forks            |                                                                                                                                           |
| Support visual regression test feature help us easily in review UI change, so perfect feature |                                                                                                                                           |
| Hot module reloading (even for functional stateless components)                               |                                                                                                                                           |
|                                                                                               |
