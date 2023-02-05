# persian-calendar
React Native app to display and convert between the Gregorian and Solar Hijri calendars

## TOC
- Background
- Getting Started

## Background
TODO explain the purpose and the calendars

## Getting Started
From the PersianCalendar directory, first install the needed packages (`npm i`)
and then run `npx react-native run-ios`. Note that this will only work with the
proper XCode version installed (MacOS). The **sample build took me 6 minutes**,
for comparison. I would like to keep the build to under 15 minutes!

Run instructions for Android:
    • Have an Android emulator running (quickest way to get started), or a device connected.
    • cd "/Users/tara/github/persian-calendar/PersianCalendar" && npx react-native run-android
  
  Run instructions for iOS:
    • cd "/Users/tara/github/persian-calendar/PersianCalendar" && npx react-native run-ios
    - or -
    • Open PersianCalendar/ios/PersianCalendar.xcworkspace in Xcode or run "xed -b ios"
    • Hit the Run button
    
  Run instructions for macOS:
    • See https://aka.ms/ReactNativeGuideMacOS for the latest up-to-date instructions.

## Notes
- For converting system's current time to IR time, use [moment](https://momentjs.com/timezone/)
- To convert the Solar Hijri year into the equivalent Gregorian year add 621 or 622 years to the Solar Hijri year depending on whether the Solar Hijri year has or has not begun (*before v. after the Spring Equinox, typically 21 March*).
- [multi-language react app tutorial](https://betterprogramming.pub/creating-a-multi-language-app-in-react-native-9828b138c274)
- Color palette: #2e5f23, #4a6e54, #667b80, #8188ac, #9e93d9
![palette image](colorkit.png)


What follows from here are copies of important parts of the Medium articles (since I'm worried I won't have access to them later):
### React Native Calendar
`$ npm install --save react-native-calendars`

```
import {Calendar, CalendarList, Agenda} from 'react-native-calendars';

{
    day: 1,         // day of the month (i.e. 1-31)
    month: 1,       // month of the year (i.e. 1-12)
    year: 2017,     //year
    timestamp,      // UTC timestamp representing 00:00 AM of the date dateString: '2016-05-13'     // date formatted as 'YYYY-MM-DD' string
}
```

Vertical calendar
```
import React from 'react';
import {
    Dimensions,
    View,
    StyleSheet
} from 'react-native';
import EventCalendar from 'react-native-events-calendar';

let { width } = Dimensions.get('window');

export default class App extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            events:
            {
                start: '2020-01-01 00:00:00',
                end: '2020-01-01 02:00:00',
                title: 'New Year Celebration Party',
                summary: 'Hotel Radision',
            },
            {
            start: '2020-01-01 01:00:00',
            end: '2020-01-01 02:00:00',
            title: 'New Year Wishes',
            summary: 'Call & Text Everyone',
            },
            {
            start: '2020-01-02 00:30:00',
            end: '2020-01-02 01:30:00',
            title: 'Rahul Birthday Party',
            summary: 'Call him',
            }
        }
    };
}
eventClicked(event) {
    alert(JSON.stringify(event));
}
render() {
    return (
        <View style={ styles.container }>
            <EventCalendar eventTapped={ this.eventClicked.bind(this) } events={ this.state.events }
                width={ width } size={ 60 }
                initDate={ '2020-01-01' } scrollToFirst
            />
        </View>
    );
}
    }
const styles = StyleSheet.create({
    container: {
        flex: 1,
        backgroundColor: '#ffb3bb', marginTop: 100,
    },
});
```

### Localization
[Tutorial Webpage Link](https://betterprogramming.pub/creating-a-multi-language-app-in-react-native-9828b138c274)
`$ npm i react-native-localize`
Create a `translations` directory inside `src` and then create a JSON file for each language: en.json for English, fa.json for Farsi/Persian.

The key will be the same for each language and will be used in the app to display text. The value will be the text that we want to show to the user and will be different for each language.

in App.js,
```
import * as RNLocalize from "react-native-localize";
import memoize from "lodash.memoize"; // Use for caching/memoize for better performance
```

Helper material
```
const translationGetters = {
  // lazy requires (metro bundler does not support symlinks)
  ar: () => require("./src/translations/ar.json"),
  en: () => require("./src/translations/en.json"),
  fr: () => require("./src/translations/fr.json")
};

const translate = memoize(
  (key, config) => i18n.t(key, config),
  (key, config) => (config ? key + JSON.stringify(config) : key)
);

const setI18nConfig = () => {
  // fallback if no available language fits
  const fallback = { languageTag: "en", isRTL: false };

  const { languageTag, isRTL } =
    RNLocalize.findBestAvailableLanguage(Object.keys(translationGetters)) ||
    fallback;

  // clear translation cache
  translate.cache.clear();
  // update layout direction
  I18nManager.forceRTL(isRTL);
  // set i18n-js config
  i18n.translations = { [languageTag]: translationGetters[languageTag]() };
  i18n.locale = languageTag;
};
```

App class component
```
export default class App extends React.Component {
  constructor(props) {
    super(props);
    setI18nConfig(); // set initial config
  }

  componentDidMount() {
    RNLocalize.addEventListener("change", this.handleLocalizationChange);
  }

  componentWillUnmount() {
    RNLocalize.removeEventListener("change", this.handleLocalizationChange);
  }

  handleLocalizationChange = () => {
    setI18nConfig();
    this.forceUpdate();
  };

  render() {
    return (
      <SafeAreaView style={styles.safeArea}>
        <Text style={styles.value}>{translate("hello")}</Text>
      </SafeAreaView>
    );
  }
}

const styles = StyleSheet.create({
  safeArea: {
    backgroundColor: "white",
    flex: 1,
    alignItems: "center",
    justifyContent: "center"
  },
  value: {
    fontSize: 18
  }
});
```

In our constructor method, we called setI18nConfig() which will set the initial configuration.

Then in componentDidMount(), we’ll add an event listener which will listen for any changes and call handleLocalizationChange() if any changes occur.

The handleLocalizationChange() method fires setI18nConfig() and forceUpdate(). This is necessary for Android devices, as the component needs to be re-render for the changes to be visible.

We will remove the listener in componentWillUnmount() lifecycle method.

Finally, in the render() we’ll return hello by using translate() and passing the key as a parameter into it. It will then automatically figure out the language and the text that needs to be shown for that language.
