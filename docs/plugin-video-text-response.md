# plugin-video-text-response

Plays a video and collects one or more free-text responses from the participant. By default,
the response box and submit button are only enabled while the video is paused (via a custom
Pause/Resume button and/or a configurable key, default: spacebar) — pausing opens a "response
window," submitting closes it again until the next pause, and this can repeat as many times as
the participant likes during a single trial. Native HTML5 video controls (which include a seek
bar) are intentionally never used, so the participant can pause/resume but cannot scrub or
change the playback position.

## Parameters

In addition to the [parameters available in all plugins](https://www.jspsych.org/latest/overview/plugins#parameters-available-in-all-plugins), this plugin accepts the following parameters. Parameters with a default value of undefined must be specified. Other parameters can be left unspecified if the default value is acceptable.

| Parameter                       | Type        | Default Value | Description                                                                                                                                                  |
| -------------------------------- | ----------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| stimulus                         | VIDEO       | *undefined*    | Array of file paths to the video, in multiple formats for cross-browser support. The first format the browser can play is used.                          |
| width                             | INT         | null           | The width of the video in pixels.                                                                                                                          |
| height                            | INT         | null           | The height of the video in pixels.                                                                                                                          |
| autoplay                         | BOOL        | true           | If true, the video begins playing as soon as it has loaded.                                                                                                  |
| show_pause_button                | BOOL        | true           | If true, a custom Pause/Resume button is displayed below the video.                                                                                          |
| pause_key                        | KEY         | `" "`          | A key that toggles pause/resume when pressed, in addition to (or instead of) `show_pause_button`. Set to `null` to disable keyboard pausing entirely.       |
| start                             | FLOAT       | null           | Time, in seconds, to start the clip. If null, the video starts at the beginning.                                                                            |
| stop                              | FLOAT       | null           | Time, in seconds, to stop the clip. If null, the video plays to the end.                                                                                    |
| rate                              | FLOAT       | 1              | The playback rate of the video. 1 is normal speed.                                                                                                           |
| trial_ends_after_video           | BOOL        | false          | If true, the trial ends automatically as soon as the video finishes playing.                                                                                 |
| trial_duration                   | INT         | null           | The maximum time, in milliseconds, to wait before ending the trial. If null, there is no deadline.                                                          |
| prompt                            | HTML_STRING | null           | HTML content (e.g. instructions) displayed below the video and above the text response box.                                                                |
| response_allowed_while_playing   | BOOL        | false          | If false, the response box and submit button are only enabled while the video is paused. If true, the box stays enabled continuously.                       |
| enable_response_after            | INT         | 0              | How long, in milliseconds, after the *first* response window opens before the text box and submit button are actually enabled. Later windows aren't delayed. |
| placeholder                      | STRING      | ""             | Placeholder text shown in the empty text response box.                                                                                                       |
| rows                              | INT         | 1              | The number of visible text rows in the response box.                                                                                                          |
| button_label                     | STRING      | "submit"       | Label displayed on the submit button.                                                                                                                        |
| required                          | BOOL        | true          | If true, blocks submission of an empty or whitespace-only response.                                                                                          |
| allow_numbers                    | BOOL        | true           | If false, number characters (0-9) are stripped from the response box as they're typed.                                                                      |
| allow_letters                    | BOOL        | true           | If false, letter characters are stripped from the response box as they're typed.                                                                            |
| allow_symbols                    | BOOL        | true           | If false, symbol/punctuation characters are stripped from the response box as they're typed.                                                                |
| show_response_history            | BOOL        | false          | If true, previously submitted responses are displayed on screen (as a running list) during the trial.                                                       |
| response_history_limit           | INT         | null           | Maximum number of past responses shown in the history list at one time. When the limit is reached, the oldest entry is removed as each new one is added. Only applies when `show_response_history` is true. If null, all responses are shown. |
| one_response_per_pause           | BOOL        | true           | Only applies when `response_allowed_while_playing` is false. If true (default), submitting a response closes the response box until the next pause. If false, the box clears and stays open after each submission, allowing multiple responses within the same pause. |
| response_ends_trial              | BOOL        | true          | If true, the trial ends as soon as the participant submits a response.                                                                                       |
| show_done_button                 | BOOL        | false          | If true, a button is shown below the response box that ends the trial when clicked.                                                                          |
| done_button_label                | STRING      | "Continue"     | Label for the done button. Only applies when `show_done_button` is true.                                                                                     |

## Data Generated

In addition to the [default data collected by all plugins](https://www.jspsych.org/latest/overview/plugins#data-collected-by-all-plugins), this plugin collects the following data for each trial.

| Name              | Type           | Value                                                                                                                                                                |
| ----------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| response           | array of STRING | The text entered by the participant for each response window, in submission order. One entry per pause-respond cycle.                                            |
| rt                  | array of INT     | Time, in milliseconds, from the start of the trial until each response was submitted. Same length and order as `response`.                                        |
| response_duration         | array of INT     | Time, in milliseconds, from when each response window became enabled (i.e. the relevant pause) until that response was submitted. Same length and order as `response`. |
| response_video_time | array of FLOAT   | The video's playback position (in seconds) at the moment each response was submitted. In gated mode the video is still paused at this point, so this directly links each response to its location in the video. Same length and order as `response`. |
| stimulus            | array of STRING | The video file(s) that were displayed during the trial.                                                                                                            |
| pause_video_time   | array of FLOAT   | For each pause, the video's playback position (in seconds) at the moment it was paused. One entry per pause, independent of whether a response was submitted.    |
| pause_duration      | array of INT     | For each pause, how long it lasted, in milliseconds, from the pause event to the next resume (or to the end of the trial, if it ended while still paused).        |
| total_trial_time    | INT             | The total elapsed time, in milliseconds, from the start of the trial to the end of the trial.                                                                       |

`rt` and `response_duration` answer different questions: `rt` is time-since-trial-start (useful if a trial only ever has a single response, e.g. when a response ends the trial), while `response_duration` is time-since-that-response-window-opened (the more standard "how fast did they respond" latency, especially when a trial has multiple pause-respond cycles). Both always have the same length as `response`; `pause_video_time`/`pause_duration` may be a different length, since a participant can pause to think without submitting a response.

## Install

Using the CDN-hosted JavaScript file:

```html
<script src="https://unpkg.com/plugin-video-text-response"></script>
```

Using NPM:

```
npm install plugin-video-text-response
```

```js
import VideoTextResponse from "plugin-video-text-response";
```

## Examples

### Basic usage: pause to respond, as many times as needed

```javascript
var trial = {
  type: jsPsychVideoTextResponse,
  stimulus: ["video/sample_video.mp4"],
  prompt: "<p>Pause the video (button or spacebar) whenever you want to respond.</p>",
  trial_ends_after_video: true,
};
```

### Continuous responding while the video plays

```javascript
var trial = {
  type: jsPsychVideoTextResponse,
  stimulus: ["video/sample_video.mp4"],
  response_allowed_while_playing: true,
  trial_ends_after_video: true,
};
```

### Requiring a response, restricting to letters only, and showing response history

```javascript
var trial = {
  type: jsPsychVideoTextResponse,
  stimulus: ["video/sample_video.mp4"],
  required: true,
  allow_numbers: false,
  allow_symbols: false,
  show_response_history: true,
  trial_ends_after_video: true,
};
```

### Disabling the spacebar shortcut and enforcing a minimum viewing time

```javascript
var trial = {
  type: jsPsychVideoTextResponse,
  stimulus: ["video/sample_video.mp4"],
  pause_key: null,
  enable_response_after: 2000,
  trial_duration: 20000,
};
```
