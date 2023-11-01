# react-pusu 

Simple `pub-sub` implementation APIs, HOCs & Hooks using [pusu](https://www.npmjs.com/package/pusu) for [React](https://reactjs.org/) Components. `react-pusu` uses [pusu](https://www.npmjs.com/package/pusu) internally and provides an HOC for better usability. 

## Compatibility
| React Version | react-pusu Compatibility |
|--|--|
| >= React@16.8 | ^2.0.0 |
| React@15, <= React@16.7 | ^1.0.0 |

## Installation

`npm install --save pusu react-pusu`

## createPublication, publish & subscribe

Please refer [pusu](https://www.npmjs.com/package/pusu).

## withSubscribe

**Type Definition**:

```
import type { TSubscribe } from 'pusu';

type TWithSubscribe = (Component: React.ComponentType<{ subscribe: TSubscribe }>) => React.ComponentType;
```

**Parameters**:
- `Component`: *(Required)* - React Component

`withSubscribe` supplies a function `subscribe` as a property to the React Component. The Component can subscribe to the publication and can receive the data using this function, whenver the publisher publishes it.

**`withSubscribe` makes sure that all the subscriptions are removed/unsubscribed before the component is unmounted.** This way the consumer React Component can use the `props.subscribe`, even multiple times, without worrying about unsubscribing before it is unmounted.

```
import { withSubscribe } from 'react-pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

class DashboardCompanySatistics extends React.Component {
  constructor(props, context) {
    super(props, context);
    props.subscribe(refreshPageDataPublication, this.refreshData);
  }

  refreshData = ({ asOfDate }) => {
    // load the data (may be using redux)
  }
  
  render() {
    return (
      <section>
        // render the statistics here ...
      </section>
    );
  }
}

export default withSubscribe(DashboardCompanySatistics);
```

### Unsubscribing explicitely 

`props.subscribe`, when called, returns a function which can be called to unsubscribe from the publication. Sometimes component may need to unsubscribe based on some condition or action, for those cases the function returned by `props.subscribe` can be called to unsubscribe.

```
import { withSubscribe } from 'react-pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

class DashboardCompanySatistics extends React.Component {
  constructor(props, context) {
    super(props, context);

    // Assign the unsubscriber function to the class member/variable
    this.unsubscribeFromRefreshPublication = props.subscribe(refreshPageDataPublication, this.refreshData);
  }

  refreshData = ({ asOfDate }) => {
    // load the data (may be using redux)
  }

  onSomeAction = () => {
    // Unsubscribe from publication
    this.unsubscribeFromRefreshPublication();
  }
  
  render() {
    return (
      <section>
        // render the statistics here ...
      </section>
    );
  }
}

export default withSubscribe(DashboardCompanySatistics);
```

## Migrating from 1.1 to 1.2

### Breaking change

- The version 1.2 will need `pusu` as a separate dependency to be installed
- The version 1.2 will allow only one parameter while publishing the data & subscribing to the data.

### 1.1

The version 1.1 wes allowing more than one parameters while publishing the data.

In the example below, publisher is publishing date and company id as two different parameters.

```
import { publish } from 'pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

const RefreshPageDataButton = ({ company }) => (
  <button
    onClick={()=> {
      // Publish the data 
      publish(publication, new Date(), company._id);
    }}
  >
    Refresh
  </button>
);

export default RefreshPageDataButton;
```

The subscriber receives two arguments as date and company id.
 
```
import { useSubscribe } from 'react-pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

const DashboardCompanySatistics = () => {
  const subscribe = useSubscribe();
  
  useEffect(() => {
    const refreshData = (asOf, companyId) => {
      // load the data (may be using redux)
    }

    subscribe(refreshPageDataPublication, refreshData);
  }
   
  return (
    <section>
      // render the statistics here ...
    </section>
  );
};

export default DashboardCompanySatistics;
```

### 1.2

The version 1.2 will allow only one parameter while publishing the data & subscribing to the data.

In the example below, publisher is publishing one JSON object consisting of date and company id.

```
import { publish } from 'pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

const RefreshPageDataButton = ({ company }) => (
  <button
    onClick={()=> {
      // Publish the data 
      publish(publication, { asOfDate: new Date(), companyId: company._id });
    }}
  >
    Refresh
  </button>
);

export default RefreshPageDataButton;
```

The subscriber receives it as the same JSON object consisting of date and company id.

```
import { withSubscribe } from 'react-pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

class DashboardCompanySatistics extends React.Component {
  constructor(props, context) {
    super(props, context);
    props.subscribe(refreshPageDataPublication, this.refreshData);
  }

  refreshData = ({ asOfDate, companyId }) => {
    // load the data (may be using redux)
  }
  
  render() {
    return (
      <section>
        // render the statistics here ...
      </section>
    );
  }
}

export default withSubscribe(DashboardCompanySatistics);
```

```
import { useSubscribe } from 'react-pusu';
import refreshPageDataPublication from './publications/refresh-page-data-publication';

const DashboardCompanySatistics = () => {
  const subscribe = useSubscribe();
  
  useEffect(() => {
    const refreshData = ({ asOfDate, companyId }) => {
      // load the data (may be using redux)
    }

    subscribe(refreshPageDataPublication, refreshData);
  }
   
  return (
    <section>
      // render the statistics here ...
    </section>
  );
};

export default DashboardCompanySatistics;
```

## License

MIT
