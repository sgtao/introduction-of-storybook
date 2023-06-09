[to Top](../../README.md)

# 21_IntroToStorybook
- Storybook for React tutorial
  - Set up Storybook in your development environment
  - refer : https://storybook.js.org/tutorials/intro-to-storybook/react/ja/get-started/

## 構成
- 01．[はじめに](#はじめに)
- 02．[単純なコンポーネント](#単純なコンポーネント)
- 03．[複合的なコンポーネント](#複合的なコンポーネント)
- 04．[データを繋ぐ](#データを繋ぐ)
- 05．[画面を作る](#画面を作る)
- 06．[デプロイ](#デプロイ)
- 07．[テスト](#テスト)
- 08．[アドオン](#アドオン)
- 09．[まとめ](#まとめ)
- 10．[貢献する](#貢献する)


## はじめに
[to Top](#)

- 開発環境に Storybook を導入しましょう
  - Storybook は開発時にアプリケーションと並行して動きます。
  - Storybook を使用することで、UI コンポーネントをビジネスロジックやコンテキストから切り離して開発できるようになります。

### React 向けの Storybook を構築する

#### インストール
- 次のコマンドを実行して開発環境を準備しましょう：
```shell
# Clone the template
npx degit chromaui/intro-storybook-react-template taskbox
#
cd taskbox
#
# Install dependencies
yarn
```

#### 動作確認

- さまざまな環境が動くことを次のコマンドで確認しましょう:
- 自動テスト（`w`でメニュー表示、`q`で停止）
  - testはなくなってた
```shell
# Run the test runner (Jest) in a terminal:
# yarn test --watchAll
```
- storybookの起動
  * コンパイル後、port:6006にアクセス
```shell
# Start the component explorer on port 6006:
yarn storybook
```
- アプリの起動
  * コンパイル後、port:3000にアクセス
```shell
# Run the frontend app proper on port 3000:
# yarn start
yarn dev
```

| storybook起動 | アプリ起動 |
|-----|-----|
| `yarn storybook`でブラウザ起動される | `yarn dev`後にブラウザアクセス |
| ![image](./images/011_yarn-storybook.png) | ![image](./images/012_yarn-dev-app.png) |


#### storybookの実行ログ
```sh
$ yarn storybook
...
info => Serving static files from ././public at /
info => Starting manager..
╭─────────────────────────────────────────────────────────────────────╮
│                                                                     │
│   Storybook 7.0.6 for react-vite started                            │
│   1.13 min for manager and 5.95 min for preview                     │
│                                                                     │
│   Local:            http://localhost:6006/                          │
│   On your network:  http://xxx.xxx.xxx.xxx:6006/                     │
│                                                                     │
│   A new version (7.0.11) is available!                              │
│                                                                     │
│   Upgrade now: npx storybook@latest upgrade                         │
│                                                                     │
│   Read full changelog:                                              │
│   https://github.com/storybookjs/storybook/blob/next/CHANGELOG.md   │
│                                                                     │
╰─────────────────────────────────────────────────────────────────────╯
```


### 変更をコミットする
- 動作確認ができたら、レポジトリにコミット
```shell
# git init # at 1st time
git add .
# git commit -m "first commit"
git commit -m "add 01_IntroToStorybook and taskbox first commit"
git branch -M main
```

## 単純なコンポーネント
[to Top](#)

- 単純なコンポーネントを切り離して作りましょう
  * タスクのコンポーネントと、対応するストーリーファイルを作成する

### コンポーネント：Task (タスク)
- `Task` は今回作るアプリケーションのコアとなるコンポーネント
  * title – タスクを説明する文字列
  * state – タスクがどのリストに存在するか。またチェックされているかどうか。

```JavaScript
// src/components/Task.jsx
import React from 'react';

export default function Task({ task: { id, title, state }, onArchiveTask, onPinTask }) {
  return (
    <div className="list-item">
      <input type="text" value={title} readOnly={true} />
    </div>
  );
}
```

### セットアップする

- ストーリーファイル
```JavaScript
// src/components/Task.stories.jsx
import React from 'react';
import Task from './Task';
//
export default {
  component: Task,
  title: 'Task',
};
//
const Template = args => <Task {...args} />;
//
export const Default = Template.bind({});
Default.args = {
  task: {
    id: '1',
    title: 'Test Task',
    state: 'TASK_INBOX',
    updatedAt: new Date(2021, 0, 1, 9, 0),
  },
};
//
export const Pinned = Template.bind({});
Pinned.args = {
  task: {
    ...Default.args.task,
    state: 'TASK_PINNED',
  },
};
//
export const Archived = Template.bind({});
Archived.args = {
  task: {
    ...Default.args.task,
    state: 'TASK_ARCHIVED',
  },
};
//
// EOF
```

#### `App.jsx`の更新
- テンプレート`App.jsx`の内容を一度クリアする
```js
import './App.css'

function App() {
  return (
    <div className="App">
      {Task}
    </div>
  )
}

export default App
```

### 設定する
- 作成したストーリーを認識させ、CSS ファイルを使用できるようにするため、Storybook の設定をいくつか変更する
```JavaScript
// .storybook/main.js
/** @type { import('@storybook/react-vite').StorybookConfig } */
const config = {
  // stories: ["../src/**/*.stories.mdx", "../src/**/*.stories.@(js|jsx|ts|tsx)"],
  stories: ['../src/components/**/*.stories.jsx'],
  staticDirs: ["../public"],
  addons: [
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@storybook/addon-interactions",
  ],
  framework: {
    name: "@storybook/react-vite",
    options: {},
  },
  docs: {
    autodocs: "tag",
  },
};
export default config;
```

- `.storybook/preview.js`も変更する
```JavaScript
// .storybook/preview.js
/** @type { import('@storybook/react').Preview } */
import '../src/index.css'; // append import
//
const preview = {
  parameters: {
    actions: { argTypesRegex: "^on[A-Z].*" },
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/,
      },
    },
  },
};
export default preview;
```


#### 動作確認

| storybook起動 | アプリ起動 |
|-----|-----|
| ![image](./images/021_storybook.png) | ![image](./images/022_reactApp.png) |



### 状態を作り出す
- Taskコンポーネントのスタイリングをするため、状態による変化をつける
```JavaScript
// src/components/Task.jsx
import React from 'react';

export default function Task({ task: { id, title, state }, onArchiveTask, onPinTask }) {
  return (
    <div className={`list-item ${state}`}>
      <label
        htmlFor="checked"
        aria-label={`archiveTask-${id}`}
        className="checkbox"
      >
        <input
          type="checkbox"
          disabled={true}
          name="checked"
          id={`archiveTask-${id}`}
          checked={state === "TASK_ARCHIVED"}
        />
        <span
          className="checkbox-custom"
          onClick={() => onArchiveTask(id)}
        />
      </label>

      <label htmlFor="title" aria-label={title} className="title">
        <input
          type="text"
          value={title}
          readOnly={true}
          name="title"
          placeholder="Input title"
        />
      </label>

      {state !== "TASK_ARCHIVED" && (
        <button
          className="pin-button"
          onClick={() => onPinTask(id)}
          id={`pinTask-${id}`}
          aria-label={`pinTask-${id}`}
          key={`pinTask-${id}`}
        >
          <span className={`icon-star`} />
        </button>
      )}
    </div>
  );
}
```

#### 動作確認
- styling後のstorybook：

| storybook Task(Pinned) | storybook Task(Archived) |
|-----|-----|
| 左メニューの`Pinned`選択 | メニュー`Archived`選択 |
| ![image](./images/023_storybook-Task-Pinned.png) | ![image](./images/024_storybook-Task-Archived.png) |



### データ要件を明示する
- PropTypes を用いた型チェックを追加する
  - 関連情報：https://ja.reactjs.org/docs/typechecking-with-proptypes.html
  - `Task.jsx`に下を追加することで、タスクコンポーネントの誤使用された際、警告が表示されます。
```JavaScript
// src/components/Task.js
import React from 'react';
+ import PropTypes from 'prop-types'; // added import
//
const Task = ({..})=>{
  ...
}
//
// 下を追記：
Task.propTypes = {
    /** Composition of the task */
    task: PropTypes.shape({
        /** Id of the task */
        id: PropTypes.string.isRequired,
        /** Title of the task */
        title: PropTypes.string.isRequired,
        /** Current state of the task */
        state: PropTypes.string.isRequired,
    }),
    /** Event to change the task to archived */
    onArchiveTask: PropTypes.func,
    /** Event to change the task to pinned */
    onPinTask: PropTypes.func,
};
//
export default Task;
```

### アクセシビリティの問題の検知
- アクセシビリティテストとは、[WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) のルールなどに基づき、自動化ツールでDOM を監視します。

#### アドオンインストール
- 以下のコマンドでアドオンをインストール
```sh
yarn add --dev @storybook/addon-a11y
```

#### アドオンを利用可能にする
```js
// .storybook/main.js

module.exports = {
  stories: ['../src/components/**/*.stories.js'],
  staticDirs: ['../public'],
  addons: [
    '@storybook/addon-links',
    '@storybook/addon-essentials',
    '@storybook/preset-create-react-app',
    '@storybook/addon-interactions',
+   '@storybook/addon-a11y',
  ],
  framework: '@storybook/react',
  core: {
    builder: '@storybook/builder-webpack5',
  },
  features: {
    interactionsDebugger: true,
  },
};
```

#### アクセシビリティの問題
- storybookを再実行すると、Archivedメニューでエラー発生
  * 「Elements must have sufficient color contrast」はコントラスト不足を指摘されてる

![image](./images/025_storybook-accesibilityViolate.png)


#### 対処方法
- テキストカラーをより暗いグレーに修正する

```js
// src/index.css
...
.list-item.TASK_ARCHIVED input[type="text"] {
  /* color: #a0aec0; */
  color: #4a5568;
  text-decoration: line-through;
}
...
```

- storybookを再実行すると、ArchivedメニューのAccessibilityのエラー解消される
![image](./images/026_recover-from-accesibilityViolate.png)



## 複合的なコンポーネント
[to Top](#)

- シンプルコンポーネントから複合的コンポーネントを組み立てる

### TaskList (タスクリスト)
- いくつかのTaskコンポーネントを組み合わせたものをタスクリストとする
  - Taskコンポーネントの複数状態を組み合わせると「通常タスクのみ」「ピン留めありタスク」の組み合わせができる
  - タスクデータが送信中の状態や、タスクが０の状態（空の状態）などを複合的に作れる

<br>

- 初期状態とPINNED状態
![image](https://storybook.js.org/tutorials/intro-to-storybook/tasklist-states-1.png)

<br>

- EMPTY状態とLOADING状態
![image](https://storybook.js.org/tutorials/intro-to-storybook/tasklist-states-2.png)

### セットアップする
- TaskList のコンポーネントとそのストーリーファイルを追加する

```JavaScript
// src/components/TaskList.jsx
// TaskList.jsx
import React from 'react';

import Task from './Task';

export default function TaskList({ loading, tasks, onPinTask, onArchiveTask }) {
  const events = {
    onPinTask,
    onArchiveTask,
  };

  if (loading) {
    return <div className="list-items">loading</div>;
  }

  if (tasks.length === 0) {
    return <div className="list-items">empty</div>;
  }

  return (
    <div className="list-items">
      {tasks.map(task => (
        <Task key={task.id} task={task} {...events} />
      ))}
    </div>
  );
}
```

- ストーリーファイルも追加する
  *  [デコレーター](https://storybook.js.org/docs/react/writing-stories/decorators)を使ってストーリーに任意のラッパーを設定できます。
```JavaScript
// TaskList.stories.jsx

import React from 'react';

import TaskList from './TaskList';
import * as TaskStories from './Task.stories';

export default {
  component: TaskList,
  title: 'TaskList',
  decorators: [story => <div style={{ padding: '3rem' }}>{story()}</div>],
};

const Template = args => <TaskList {...args} />;

export const Default = Template.bind({});
Default.args = {
  // Shaping the stories through args composition.
  // The data was inherited from the Default story in Task.stories.js.
  tasks: [
    { ...TaskStories.Default.args.task, id: '1', title: 'Task 1' },
    { ...TaskStories.Default.args.task, id: '2', title: 'Task 2' },
    { ...TaskStories.Default.args.task, id: '3', title: 'Task 3' },
    { ...TaskStories.Default.args.task, id: '4', title: 'Task 4' },
    { ...TaskStories.Default.args.task, id: '5', title: 'Task 5' },
    { ...TaskStories.Default.args.task, id: '6', title: 'Task 6' },
  ],
};

export const WithPinnedTasks = Template.bind({});
WithPinnedTasks.args = {
  // Shaping the stories through args composition.
  // Inherited data coming from the Default story.
  tasks: [
    ...Default.args.tasks.slice(0, 5),
    { id: '6', title: 'Task 6 (pinned)', state: 'TASK_PINNED' },
  ],
};

export const Loading = Template.bind({});
Loading.args = {
  tasks: [],
  loading: true,
};

export const Empty = Template.bind({});
Empty.args = {
  // Shaping the stories through args composition.
  // Inherited data coming from the Loading story.
  ...Loading.args,
  loading: false,
};
```

#### 動作確認

- 初回コードの状態
  * Taskコンポーネントを並べた状態

| storybook TaskList(Default) | storybook TaskList(Loading) |
|-----|-----|
| ![image](./images/031_initial-tasklist-default.png) | ![image](./images/032_initial-tasklist-loading.png) |



### 状態を作りこむ
- TaskListコンポーネントは、LoadingとEmptyの状態・スタイルをすることで表現の幅が広がる
  - `TaskList.js`のスタイリングを追加する
  - データ要件とプロパティも追加
```JavaScript
// TaskList.jsx
import React from 'react';
import PropTypes from 'prop-types';

import Task from './Task';

export default function TaskList({ loading, tasks, onPinTask, onArchiveTask }) {
  const events = {
    onPinTask,
    onArchiveTask,
  };
  const LoadingRow = (
    <div className="loading-item">
      <span className="glow-checkbox" />
      <span className="glow-text">
        <span>Loading</span> <span>cool</span> <span>state</span>
      </span>
    </div>
  );
  if (loading) {
    return (
      <div className="list-items" data-testid="loading" key={"loading"}>
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
      </div>
    );
  }
  if (tasks.length === 0) {
    return (
      <div className="list-items" key={"empty"} data-testid="empty">
        <div className="wrapper-message">
          <span className="icon-check" />
          <div className="title-message">You have no tasks</div>
          <div className="subtitle-message">Sit back and relax</div>
        </div>
      </div>
    );
  }

  const tasksInOrder = [
    ...tasks.filter((t) => t.state === "TASK_PINNED"),
    ...tasks.filter((t) => t.state !== "TASK_PINNED"),
  ];
  return (
    <div className="list-items">
      {tasksInOrder.map((task) => (
        <Task key={task.id} task={task} {...events} />
      ))}
    </div>
  );
}

// check properties
TaskList.propTypes = {
    /** Checks if it's in loading state */
    loading: PropTypes.bool,
    /** The list of tasks */
    tasks: PropTypes.arrayOf(Task.propTypes.task).isRequired,
    /** Event to change the task to pinned */
    onPinTask: PropTypes.func,
    /** Event to change the task to archived */
    onArchiveTask: PropTypes.func,
 };
 TaskList.defaultProps = {
    loading: false,
};
```

#### 動作確認
- スタリング後のstorybook

| storybook TaskList(Loading) | storybook TaskList(Empty) |
|-----|-----|
| ![image](./images/034_styled-tasklist-loading.png) | ![image](./images/035_styled-tasklist-empty.png) |




## データを繋ぐ
[to Top](#)

- UI コンポーネントとデータを繋ぐ方法を学びましょう。

### 繋がれたコンポーネント
- TaskList コンポーネントは「presentational (表示用)」として書かれてるので、データを渡すにはデータプロバイダに繋ぐ必要があります。
  - ここでは[Redux](https://redux.js.org/)を使用し、アプリケーションにシンプルなデータモデルを作ります。

```shell
yarn add @reduxjs/toolkit react-redux
```

- Redux のストアを作るため、`src/lib`フォルダの`store.js`というファイルを作ります (あえて簡単にしています):
```JavaScript
// src/lib/store.jsx
/* A simple redux store/actions/reducer implementation.
 * A true app would be more complex and separated into different files.
 */
import { configureStore, createSlice } from '@reduxjs/toolkit';

/*
 * The initial state of our store when the app loads.
 * Usually, you would fetch this from a server. Let's not worry about that now
 */
const defaultTasks = [
    { id: '1', title: 'Something', state: 'TASK_INBOX' },
    { id: '2', title: 'Something more', state: 'TASK_INBOX' },
    { id: '3', title: 'Something else', state: 'TASK_INBOX' },
    { id: '4', title: 'Something again', state: 'TASK_INBOX' },
];
const TaskBoxData = {
    tasks: defaultTasks,
    status: 'idle',
    error: null,
};

/*
 * The store is created here.
 * You can read more about Redux Toolkit's slices in the docs:
 * https://redux-toolkit.js.org/api/createSlice
 */
const TasksSlice = createSlice({
    name: 'taskbox',
    initialState: TaskBoxData,
    reducers: {
        updateTaskState: (state, action) => {
            const { id, newTaskState } = action.payload;
            const task = state.tasks.findIndex((task) => task.id === id);
            if (task >= 0) {
                state.tasks[task].state = newTaskState;
            }
        },
    },
});

// The actions contained in the slice are exported for usage in our components
export const { updateTaskState } = TasksSlice.actions;

/*
 * Our app's store configuration goes here.
 * Read more about Redux's configureStore in the docs:
 * https://redux-toolkit.js.org/api/configureStore
 */
const store = configureStore({
    reducer: {
        taskbox: TasksSlice.reducer,
    },
});

export default store;
```

- TaskList コンポーネントで、Redux のストアに 「connect (接続)」し、ストアから、気になるタスクのリストを描画します。
  - `TaskList.jsx`を書き換える
```JavaScript
// src/components/TaskList.jsx
import React from 'react';
import Task from './Task';
import { useDispatch, useSelector } from 'react-redux';
import { updateTaskState } from '../lib/store';

export default function TaskList() {
  // We're retrieving our state from the store
  const tasks = useSelector((state) => {
    const tasksInOrder = [
      ...state.taskbox.tasks.filter((t) => t.state === 'TASK_PINNED'),
      ...state.taskbox.tasks.filter((t) => t.state !== 'TASK_PINNED'),
    ];
    const filteredTasks = tasksInOrder.filter(
      (t) => t.state === 'TASK_INBOX' || t.state === 'TASK_PINNED'
    );
    return filteredTasks;
  });

  const { status } = useSelector((state) => state.taskbox);

  const dispatch = useDispatch();

  const pinTask = (value) => {
    // We're dispatching the Pinned event back to our store
    dispatch(updateTaskState({ id: value, newTaskState: 'TASK_PINNED' }));
  };
  const archiveTask = (value) => {
    // We're dispatching the Archive event back to our store
    dispatch(updateTaskState({ id: value, newTaskState: 'TASK_ARCHIVED' }));
  };
  const LoadingRow = (
    <div className="loading-item">
      <span className="glow-checkbox" />
      <span className="glow-text">
        <span>Loading</span> <span>cool</span> <span>state</span>
      </span>
    </div>
  );
  if (status === 'loading') {
    return (
      <div className="list-items" data-testid="loading" key={"loading"}>
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
      </div>
    );
  }
  if (tasks.length === 0) {
    return (
      <div className="list-items" key={"empty"} data-testid="empty">
        <div className="wrapper-message">
          <span className="icon-check" />
          <div className="title-message">You have no tasks</div>
          <div className="subtitle-message">Sit back and relax</div>
        </div>
      </div>
    );
  }

  return (
    <div className="list-items" data-testid="success" key={"success"}>
      {tasks.map((task) => (
        <Task
          key={task.id}
          task={task}
          onPinTask={(task) => pinTask(task)}
          onArchiveTask={(task) => archiveTask(task)}
        />
      ))}
    </div>
  );
}
```

#### 動作確認

- 起動はできるが、まだ操作に対する反応はしない
  * ⇒　ほんと？

| 01．起動直後 | 02．操作後 |
|-----|-----|
| 起動直後（Default） | チェックボックスクリック⇒反応しない |
| ![image](./images/041_initial-tasklist-default.png) | ![image](./images/042_clicked-tasklist-default.png) |

### デコレーターにコンテキストを渡す
- storybookの~~エラー~~解決のため、デコレーターに頼ることができ、Storybook の中でモックストアを利用する

```JavaScript
// TaskList.stories.jsx
import React from 'react';

import TaskList from './TaskList';
import * as TaskStories from './Task.stories';

import { Provider } from 'react-redux';

import { configureStore, createSlice } from '@reduxjs/toolkit';

// A super-simple mock of the state of the store
export const MockedState = {
  tasks: [
    { ...TaskStories.Default.args.task, id: '1', title: 'Task 1' },
    { ...TaskStories.Default.args.task, id: '2', title: 'Task 2' },
    { ...TaskStories.Default.args.task, id: '3', title: 'Task 3' },
    { ...TaskStories.Default.args.task, id: '4', title: 'Task 4' },
    { ...TaskStories.Default.args.task, id: '5', title: 'Task 5' },
    { ...TaskStories.Default.args.task, id: '6', title: 'Task 6' },
  ],
  status: 'idle',
  error: null,
};

// A super-simple mock of a redux store
const Mockstore = ({ taskboxState, children }) => (
  <Provider
    store={configureStore({
      reducer: {
        taskbox: createSlice({
          name: 'taskbox',
          initialState: taskboxState,
          reducers: {
            updateTaskState: (state, action) => {
              const { id, newTaskState } = action.payload;
              const task = state.tasks.findIndex((task) => task.id === id);
              if (task >= 0) {
                state.tasks[task].state = newTaskState;
              }
            },
          },
        }).reducer,
      },
    })}
  >
    {children}
  </Provider>
);

export default {
  component: TaskList,
  title: 'TaskList',
  decorators: [(story) => <div style={{ padding: "3rem" }}>{story()}</div>],
  excludeStories: /.*MockedState$/,
};

const Template = () => <TaskList />;

export const Default = Template.bind({});
Default.decorators = [
  (story) => <Mockstore taskboxState={MockedState}>{story()}</Mockstore>,
];

export const WithPinnedTasks = Template.bind({});
WithPinnedTasks.decorators = [
  (story) => {
    const pinnedtasks = [
      ...MockedState.tasks.slice(0, 5),
      { id: '6', title: 'Task 6 (pinned)', state: 'TASK_PINNED' },
    ];

    return (
      <Mockstore
        taskboxState={{
          ...MockedState,
          tasks: pinnedtasks,
        }}
      >
        {story()}
      </Mockstore>
    );
  },
];

export const Loading = Template.bind({});
Loading.decorators = [
  (story) => (
    <Mockstore
      taskboxState={{
        ...MockedState,
        status: 'loading',
      }}
    >
      {story()}
    </Mockstore>
  ),
];

export const Empty = Template.bind({});
Empty.decorators = [
  (story) => (
    <Mockstore
      taskboxState={{
        ...MockedState,
        tasks: [],
      }}
    >
      {story()}
    </Mockstore>
  ),
];
```

#### 動作確認

- 操作できるようになった

| 01．起動直後 | 02．操作後 |
|-----|-----|
| 起動直後（Default） | Pinnedをクリック⇒反応 |
| ![image](./images/043_modified-story-tasklist-default.png) | ![image](./images/044_pinned-story-tasklist-default.png) |


## 画面を作る
[to Top](#)

- この章では Storybook を使用して、コンポーネントを組み合わせて画面を作り、完成度を高めていきます。

### 繋がれた画面
- 作る画面は、(Redux から自分でデータを取得する) TaskList をラップして、Redux からの error フィールドを追加するだけです。
  - まず、Redux ストア (src/lib/store.js 内) をアップデートするところから始めましょう:
```js
// src/lib/store.jsx
/* A simple redux store/actions/reducer implementation.
 * A true app would be more complex and separated into different files.
 */
import {
    configureStore,
    createSlice,
    createAsyncThunk, // add package
} from '@reduxjs/toolkit';

/*
 * The initial state of our store when the app loads.
 * Usually, you would fetch this from a server. Let's not worry about that now
 */

const TaskBoxData = {
    tasks: [],
    status: "idle",
    error: null,
};

// add fetchTasks
/*
 * Creates an asyncThunk to fetch tasks from a remote endpoint.
 * You can read more about Redux Toolkit's thunks in the docs:
 * https://redux-toolkit.js.org/api/createAsyncThunk
 */
export const fetchTasks = createAsyncThunk('todos/fetchTodos', async () => {
    const response = await fetch(
        'https://jsonplaceholder.typicode.com/todos?userId=1'
    );
    const data = await response.json();
    const result = data.map((task) => ({
        id: `${task.id}`,
        title: task.title,
        state: task.completed ? 'TASK_ARCHIVED' : 'TASK_INBOX',
    }));
    return result;
});

/*
 * The store is created here.
 * You can read more about Redux Toolkit's slices in the docs:
 * https://redux-toolkit.js.org/api/createSlice
 */
const TasksSlice = createSlice({
    name: 'taskbox',
    initialState: TaskBoxData,
    reducers: {
        updateTaskState: (state, action) => {
            const { id, newTaskState } = action.payload;
            const task = state.tasks.findIndex((task) => task.id === id);
            if (task >= 0) {
                state.tasks[task].state = newTaskState;
            }
        },
    },
    // add extraReducers
    /*
     * Extends the reducer for the async actions
     * You can read more about it at https://redux-toolkit.js.org/api/createAsyncThunk
     */
    extraReducers(builder) {
        builder
            .addCase(fetchTasks.pending, (state) => {
                state.status = 'loading';
                state.error = null;
                state.tasks = [];
            })
            .addCase(fetchTasks.fulfilled, (state, action) => {
                state.status = 'succeeded';
                state.error = null;
                // Add any fetched tasks to the array
                state.tasks = action.payload;
            })
            .addCase(fetchTasks.rejected, (state) => {
                state.status = 'failed';
                state.error = "Something went wrong";
                state.tasks = [];
            });
    },
});

// The actions contained in the slice are exported for usage in our components
export const { updateTaskState } = TasksSlice.actions;

/*
 * Our app's store configuration goes here.
 * Read more about Redux's configureStore in the docs:
 * https://redux-toolkit.js.org/api/configureStore
 */
const store = configureStore({
    reducer: {
        taskbox: TasksSlice.reducer,
    },
});

export default store;
```

- InboxScreen.js を src/components ディレクトリに作成しましょう:
```js
// src/components/InboxScreen.jsx
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchTasks } from '../lib/store';
import TaskList from './TaskList';

export default function InboxScreen() {
    const dispatch = useDispatch();
    // We're retrieving the error field from our updated store
    const { error } = useSelector((state) => state.taskbox);
    // The useEffect triggers the data fetching when the component is mounted
    useEffect(() => {
        dispatch(fetchTasks());
    }, []);

    if (error) {
        return (
            <div className="page lists-show">
                <div className="wrapper-message">
                    <span className="icon-face-sad" />
                    <div className="title-message">Oh no!</div>
                    <div className="subtitle-message">Something went wrong</div>
                </div>
            </div>
        );
    }
    return (
        <div className="page lists-show">
            <nav>
                <h1 className="title-page">
                    <span className="title-wrapper">Taskbox</span>
                </h1>
            </nav>
            <TaskList />
        </div>
    );
}
```

- さらに、App コンポーネントを InboxScreen を描画するように変更します：
```js
// src/App.jsx
import './index.css';
import store from './lib/store';

import { Provider } from 'react-redux';
import InboxScreen from './components/InboxScreen';

function App() {
  return (
    <Provider store={store}>
      <InboxScreen />
    </Provider>
  );
}
export default App;
```


- InboxScreen.stories.jsx でストーリーを設定します:
```js
// src/components/InboxScreen.stories.jsx
import React from 'react';

import InboxScreen from './InboxScreen';
import store from '../lib/store';

import { Provider } from 'react-redux';

export default {
  component: InboxScreen,
  title: 'InboxScreen',
  decorators: [(story) => <Provider store={store}>{story()}</Provider>],
};

const Template = () => <InboxScreen />;

export const Default = Template.bind({});
export const Error = Template.bind({});
```

#### 動作確認

- storybookを再起動して動作確認
  * React Appも稼働できるので、`yarn dev`を`r`でRestartする

| React App | storybook |
|-----|-----|
| ![image](./images/051_react-app-tasklist.png) | ![image](./images/052_storybook-inbox-screen.png) |

### API をモックする
- [Mock Service Worker](https://mswjs.io/) と [Storybook's MSW addon](https://storybook.js.org/addons/msw-storybook-addon) を使用して、API をモックします
  * Mock Service Worker は、API モックライブラリです

#### パッケージインストール
- 下のコマンドを実行し、public フォルダの中にサービスワーカーを生成します。
```shell
yarn init-msw
```

- 実行ログ
```sh
$ yarn init-msw
yarn run v1.22.19
$ msw init public/
Initializing the Mock Service Worker at "/mnt/d/home/shogo/idea/2023_idea/20230503_storybook-trial/11_introduction-of-storybook/20_Guide/21_IntroToStorybook/taskbox/public"...

Service Worker successfully created!
/mnt/d/home/shogo/idea/2023_idea/20230503_storybook-trial/11_introduction-of-storybook/20_Guide/21_IntroToStorybook/taskbox/public/mockServiceWorker.js

Continue by creating a mocking definition module in your application:

https://mswjs.io/docs/getting-started/mocks

INFO In order to ease the future updates to the worker script,
we recommend saving the path to the worker directory in your package.json.

? Do you wish to save "public" as the worker directory? Yes
Writing "msw.workerDirectory" to "/mnt/d/home/shogo/idea/2023_idea/20230503_storybook-trial/11_introduction-of-storybook/20_Guide/21_IntroToStorybook/taskbox/package.json"...
Done in 16.25s.
```

#### セットアップ
- .storybook/preview.js をアップデートしてそれらを初期化する
```js
// .storybook/preview.js
/** @type { import('@storybook/react').Preview } */
import '../src/index.css';

// Registers the msw addon
import { initialize, mswDecorator } from 'msw-storybook-addon'; // add package

// Initialize MSW
initialize(); // add initialzise

// Provide the MSW addon decorator globally
export const decorators = [mswDecorator]; // add decorator

//👇 Configures Storybook to log the actions( onArchiveTask and onPinTask ) in the UI.
export const parameters = {
  actions: { argTypesRegex: '^on[A-Z].*' },
  controls: {
    matchers: {
      color: /(background|color)$/i,
      date: /Date$/,
    },
  },
};

```

- InboxScreen のストーリーを更新し、リモート API 呼び出しをモックする parameter を組み込みます:
  * JSONデータは[JSON Placeholder](https://jsonplaceholder.typicode.com/)から取得
```js
// src/components/InboxScreen.stories.jsx
import React from 'react';

import InboxScreen from './InboxScreen';
import store from '../lib/store';
import { rest } from 'msw'; // add package
import { MockedState } from './TaskList.stories'; // add package
import { Provider } from 'react-redux';

export default {
    component: InboxScreen,
    title: 'InboxScreen',
    decorators: [(story) => <Provider store={store}>{story()}</Provider>],
};

const Template = () => <InboxScreen />;

export const Default = Template.bind({});
// add Default.parameters
Default.parameters = {
    msw: {
        handlers: [
            rest.get(
                'https://jsonplaceholder.typicode.com/todos?userId=1',
                (req, res, ctx) => {
                    return res(ctx.json(MockedState.tasks));
                }
            ),
        ],
    },
};

export const Error = Template.bind({});
// add Error.parameters
Error.parameters = {
    msw: {
        handlers: [
            rest.get(
                'https://jsonplaceholder.typicode.com/todos?userId=1',
                (req, res, ctx) => {
                    return res(ctx.status(403));
                }
            ),
        ],
    },
};
```

#### 動作確認
- storybookを再起動して、InboxScreenの動作を確認

##### Defaultモード

- APIで取得したデータを表示している

![image](./images/053_storybook-inbox-screen-loading-mocked-api.png)

##### Errorモード

- APIリクエストに対して、403レスポンスが返っているケースの画面

![image](./images/054_storybook-inbox-screen-error-response.png)



### インタラクションテスト

- これまで、新しいストーリーを作るたびに、UIストーリーを手作業でチェックする必要もありました。
  * これは、とても大変な作業です。
  - ここでは、テストや操作を自動化する方法を紹介します

#### play 関数を使ったインタラクションテスト
- Storybook の`play`関数と`@storybook/addon-interactions`が役立ちます。
  * `play`関数はタスクが更新されたときに UI に何が起こるかを検証します
  * `@storybook/addon-interactions`は、一つ一つのステップごとに、Storybook のテストを可視化します

<br>

- 下のようにしてInboxScreen ストーリーを更新し、コンポーネント操作を追加してみましょう
```js
// src/components/InboxScreen.stories.jsx
import React from 'react';

import InboxScreen from './InboxScreen';

import store from '../lib/store';
import { rest } from 'msw';
import { MockedState } from './TaskList.stories';
import { Provider } from 'react-redux';

// add packages
import {
    fireEvent,
    within,
    waitFor,
    waitForElementToBeRemoved
} from '@storybook/testing-library';

export default {
    component: InboxScreen,
    title: 'InboxScreen',
    decorators: [(story) => <Provider store={store}>{story()}</Provider>],
};

const Template = () => <InboxScreen />;

export const Default = Template.bind({});
Default.parameters = {
    msw: {
        handlers: [
            rest.get(
                'https://jsonplaceholder.typicode.com/todos?userId=1',
                (req, res, ctx) => {
                    return res(ctx.json(MockedState.tasks));
                }
            ),
        ],
    },
};

// add Default.play
Default.play = async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    // Waits for the component to transition from the loading state
    await waitForElementToBeRemoved(await canvas.findByTestId('loading'));
    // Waits for the component to be updated based on the store
    await waitFor(async () => {
        // Simulates pinning the first task
        await fireEvent.click(canvas.getByLabelText('pinTask-1'));
        // Simulates pinning the third task
        await fireEvent.click(canvas.getByLabelText('pinTask-3'));
    });
};

// ... following (const)Error definition
```

##### 動作確認

- storybookを再起動してplay後の画面を確認する
  * 再度playをしたいときは、`Rerun`を指示する

| storybook起動後 | `Rerun`再実行 |
|-----|-----|
| 起動すると、`Interruction`にplay結果が表示される | 再度playする場合、`Rerun`を指示 |
| ![image](./images/055_storybook-inbox-screen-with-play.png) | ![image](./images/056_storybook-test-play-by-rerun.png) |

#### テスト自動化

- `play`関数は、ストーリーを見るときだけインタラクションテストが実行されてました。
  * コード変更時に各ストーリーを自動的にチェックしたいです。
  * テスト自動化は、Storybook の[テストランナー](https://storybook.js.org/docs/react/writing-tests/test-runner)が可能にしてくれます。
    +  [Playwright](https://playwright.dev/)パッケージにより、インタラクションテストを実行し、壊れたストーリーを検知します。

##### パッケージインストール
- 次のコマンドでインストール
```sh
yarn add --dev @storybook/test-runner
```

<br>

- test-runnerのほかにも、`playwright`スクリプトで依存パッケージのインストールが必要だった
```sh
npx playwright install-deps  
```

##### セットアップ
- `package.json`の scripts をアップデートし、新しいテストタスクを追加します。
```js
// package.json
{
  "scripts": {
    ...    
    "test-storybook": "test-storybook"
    ...    
  }
}
```

##### テスト実行
- storybookが起動している状態で、以下のコマンドを実行してください
```sh
yarn test-storybook --watch
```

<br>

- もし、storybookをカスタムURLで起動している場合は、`--url`オプションでstorybookのURLを指定する
```sh
yarn test-storybook --url http://127.0.0.1:9009
```

###### 実行ログ
```sh
$ yarn test-storybook --watch
#
# Determining test suites to run...
# スタートすると、しばらく待ってから、ストーリーを起動しているようだ
...
 RUNS   browser: chromium  src/components/InboxScreen.stories.jsx
...
```

- 画面は出てこなかった



### コンポーネント駆動開発
- [コンポーネント駆動開発](https://www.componentdriven.org/)はコンポーネント階層を上がるごとに少しずつ複雑性を拡張するアプローチです


## デプロイ
[to Top](#)

- GitHubにpushして、[Chromatic](https://www.chromatic.com/?utm_source=storybook_website&utm_medium=link&utm_campaign=storybook)でホスティングする方法を紹介
  * スキップ


## テスト
[to Top](#)

- Storybook 向けのビジュアルテスト手法を紹介
  * Chromaticを利用した方法を紹介
  * スキップ


## アドオン
[to Top](#)

- Third Partyが開発した[アドオン](https://storybook.js.org/addons)を紹介
  * スキップ

## まとめ
[to Top](#)

- Storybook のテクニックをもっと学ぶコンテンツを紹介

## 貢献する
[to Top](#)

- Storybookを広めるための協力方法を紹介

