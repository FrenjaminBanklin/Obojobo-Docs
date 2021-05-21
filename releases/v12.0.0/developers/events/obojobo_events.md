---
title: Obojobo Event Reference
menus: developers_events
---

{::comment}
Writing this document:

This is for people who don't have the ability/time to glean this info directly from the source code.
They have a data dump csv full of events they are trying to make sense of in order to build a report.
{:/comment}

Obojobo Events are used to announce and record actions.
They may be initiated on the server or client, and can be listened to in order to trigger other actions.
These events can be useful for research and data analysis. This document provides details about the different events.

Obojobo Event exports will have the following columns:

| Column | Description |
|-
| created_at | DateTime when the event was written to the database
| actor_time | DateTime when the event was initially created. This can differ from created_at when the event is initialized on the client.
| actor | Which user's session was active during this event
| action | Name of the event type (eg: `visit:create` or `nav:hide`)
| ip | IP address of the user's client.
| draft_id | Draft id of the document related to this event.
| draft_content_id | Draft Content Id of the specific version the document content this event is related to.
| version_number | [Semantic Version](https://semver.org/) of the event itself. Properties can change as Obojobo changes.
| is_preview | Boolean indicating if this event was created during an instructor preview visit or not
| visit_id | The visit id during which this event was fired
| payload | JSON data of the actual event properties (shown in the rest of this document)

> Note: Obojobo Events cannot be used on the client in {{ 'trigger' | obo_node }} and {{ 'action' | obo_node }} nodes.

## Visit

### _visit:create_

Occurs when a new visit is created. The visit is not considered started until the `visit:start` Event occurs.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| visitId | id of the visit created
| deactivatedVisitId | id of the deactivated visit

### _visit:start_

Occurs when a visit is started after its creation. Not all visits that are created are started.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| visitId | id of the visit to start

## Viewer

### _viewer:open_

Occurs when the user has opened a document in the Obojobo Viewer.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| visitId | id of the visit User/draft

> It's possible for the user to open the module in a new tab. In that case, they viewer may have "opened" but the user is not yet looking at the module. Use `viewer:initialView` for a more accurate measurement of when a user is first interacting with and viewing a module.

### _viewer:initialView_

Occurs when the module has loaded, is able to be interacted with, and the user is looking at the page.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| visitId | id of the visit User/draft

### _viewer:close_

Occurs when the user closed the browser window containing the Obojobo Viewer.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

### _viewer:inactive_

Occurs when the user has not interacted with the page in the last 10 minutes.

<dl>
	<dt>Version</dt>
	<dd>3.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| lastActiveTime | ECMAScript Date string representation of the last recorded time of interactivity
| inactiveDuration | The amount of time in milliseconds recorded with no measured interactivity (10 minutes)

### _viewer:returnFromInactive_

Occurs when the user has interacted with the page after having not interacted with the page for 10 minutes or more.

<dl>
	<dt>Version</dt>
	<dd>2.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| lastActiveTime | ECMAScript Date string representation of the last recorded time of interactivity
| inactiveDuration | The updated amount of time in milliseconds recorded with no measured interactivity
| relatedEventId | The id of the corresponding `viewer:inactive` Obojobo event

### _viewer:leave_

Occurs when the user has hidden the draft page, either by changing tabs or minimizing the window. Corresponds to the `visibilitychange` browser event and `document.hidden` browser value.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

### _viewer:return_

Occurs when the user is again viewing the draft page, either by changing tabs or re-selecting the window.

<dl>
	<dt>Version</dt>
	<dd>3.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| relatedEventId | The id of the corresponding `viewer:leave` Obojobo event, or the string `"not available"` if no corresponding related event id could be found
| leftTime | ECMAScript Date string representation of the recorded time when the tab/window went inactive
| duration | The amount of time in milliseconds recorded in which the tab/window was not active

## Question

### _question:scoreSet_

Occurs when a user recieves a score for a graded question.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | A generated UUID to represent this score
| score | The score value (0-100)
| itemId | The id of the item that was scored
| context | The context (string) when this score was set

### _question:scoreClear_

Occurs when the score for a question was reset.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | The UUID to the score that was cleared. This UUID was generated by a previous _question:scoreSet_ event for this question.
| score | The score value (0-100)
| itemId | The id of the item that was scored
| context | The context (string) when this score was cleared

### _question:showExplanation_

Occurs when the user is viewing the explanation (aka. solution) of a question.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when the explanation was shown

### _question:hideExplanation_

Occurs when the user or ViewerClient has hidden a question explanation.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| actor | Either `'user'` or `'viewerClient'`

### _question:checkAnswer_

Occurs when the user is checking their answer to a graded question.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when the user is checking their answer
| response | The state of the response of the question
| scoreId | Internal id of the question score
| score | The score value (0-100)

### _question:submitResponse_

Occurs when the user has submitted a response for an un-graded question.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when this score was set
| response | The state of the response of the question

### _question:retry_

Occurs when the user is reattempting a question. Previous responses and scores are hidden.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when this question was retried

### _question:setResponse_

Occurs when the user has selected a response to a question.

<dl>
	<dt>Version</dt>
	<dd>2.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| targetId | The id of the OboNode item that was interacted with
| response | The state of the response of the question
| context |
| assessmentId |
| attemptId |

### _question:view_

Occurs when the user has activated a question, making it visible.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when this question was viewed

### _question:hide_

Occurs when a question has been hidden.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| questionId | The id of the corresponding Question
| context | The context (string) when this question was hidden

## Assessment

### _assessment:attemptStart_

Occurs when an assessment attempt has started.

<dl>
	<dt>Version</dt>
	<dd>1.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| attemptId |
| attemptCount |

### _assessment:attemptEnd_

Occurs when an assessment attempt has ended.

<dl>
	<dt>Version</dt>
	<dd>1.3.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| attemptId |
| attemptCount |
| imported | Boolean indicating if this attempt was imported from a previous attempt
| originalScoreId | If this attempt was imported then this is the internal `assessmentScoreId` of the assessment score that was imported. Otherwise `null`.
| originalAttemptId | If this attempt was imported then this is the internal `attemptId` of the attempt that was imported. Otherwise `null`.

### _assessment:attemptScored_

Occurs when an assessment attempt has been scored.

<dl>
	<dt>Version</dt>
	<dd>2.2.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| attemptId |
| attemptCount |
| attemptScore |
| assessmentScore | `null` or a number `0` to `100` (This value is equal to `scoreDetails.assessmentModdedScore`)
| highestAssessmentScore | The most recent highest assessment score from this or any previous attempts of the same assessment
| assessmentScoreId | Internal id of this assessment score
| ltiScoreSent | `null` or a number `0` to `1` (This value was sent via an LTI replaceResult to a tool consumer)
| ltiScoreStatus | One of **LTI Status values** (see below)
| ltiStatusDetails | If an error occurred then this will be an object with error information, otherwise expect `null`
| ltiGradeBookStatus | One of **LTI Gradebook Status values** (see below)
| ltiAssessmentScoreId | Internal id of this LTI assessment score
| scoreDetails.status | One of `'passed'`, `'failed'` or `'unableToPass'`
| scoreDetails.rewardTotal | The total amount of mods rewarded (The sum of all `reward` values for each mod with an index in `rewardedMods`)
| scoreDetails.attemptScore | The attempt score - A value from `0` to `100`
| scoreDetails.rewardedMods | An array of mod indicies from the attempt rubric that were rewarded
| scoreDetails.attemptNumber |
| scoreDetails.assessmentScore | The assessment score without rewards applied
| scoreDetails.assessmentModdedScore | The final assessment score after rewards are applied
| imported | Boolean indicating if this attempt was imported from a previous attempt
| originalScoreId | If this attempt was imported then this is the internal `assessmentScoreId` of the assessment score that was imported. Otherwise `null`.
| originalAttemptId | If this attempt was imported then this is the internal `attemptId` of the attempt that was imported. Otherwise `null`.

### _assessment:attemptInvalidated_

Occurs when an assessment attempt has been invalidated because the module was updated after the user started the attempt.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| attemptId |

## Navigation

### _nav:gotoPath_

Occurs when the user has clicked on a navigation link or button and has moved to another area in the document.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| from | OboNode id that user is navigating from
| to | OboNode id that user is navigating to

### _nav:goto_

Occurs when the user has clicked a button which fires a `nav:goto` event and has now moved to another area in the document

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| from | OboNode id that user is navigating from
| to | OboNode id that user is navigating to

### _nav:prev_

Occurs when the user has clicked on the 'previous' button and has moved back one step in the document's navigation.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| from | OboNode id that user is navigating from
| to | OboNode id that user is navigating to

### _nav:next_

Occurs when the user has clicked on the 'next' button and has moved ahead one step in the document's navigation.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| from | OboNode id that user is navigating from
| to | OboNode id that user is navigating to

### _nav:lock_

Occurs when the navigation has been locked.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

### _nav:unlock_

Occurs when the navigation has been unlocked.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

### _nav:close_

Occurs when the navigation sidebar has been closed.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

### _nav:open_

Occurs when the navigation sidebar has been opened.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

None

## Media

> Currently media events are used exclusively for the IFrame and Materia OboNodes.

### _media:show_

Occurs when an IFrame or Materia widget is shown.

> This event is not fired if the IFrame is automatically loaded (Using the `autoload` property).

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | id of the OboNode shown

### _media:hide_

Occurs when an IFrame or Materia widget is hidden - Either by an event or when an OboNode (like a Page) containing an IFrame is navigated away from.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | id of the OboNode hidden
| actor | Either `'user'` or `'viewerClient'`

### _media:setZoom_

Occurs when an IFrame with the `zoom` control enabled is zoomed in or out.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | id of the OboNode where the zoom level has changed
| previousZoom | The zoom level before the `media:setZoom` event was fired
| zoom | The new zoom level

> Zoom is stored as a number > 0 representing the zoom percentage of the media. For example, `1` means the media is at `100%`, `1.5` represents `150%` and so on.

### _media:resetZoom_

Occurs when an IFrame with the `zoom` control enabled is reset to its original size.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| id | id of the OboNode where the zoom level has changed
| previousZoom | The zoom level before the `media:resetZoom` event was fired
| zoom | The new zoom level (which will be the starting zoom level when the OboNode was first shown).

> Zoom is stored as a number > 0 representing the zoom percentage of the media. For example, `1` means the media is at `100%`, `1.5` represents `150%` and so on.

## LTI

### _lti:launch_

Occurs when an LTI launch was started successfully.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| launchId |

### _lti:replaceResult_

Occurs when an LTI [replaceResult](https://www.imsglobal.org/specs/ltiomv1p0/specification#toc-4) message is sent to alter the user's score in LMS (aka Tool Consumer). This event is created regardless of the ReplaceResult being successful.

<dl>
	<dt>Version</dt>
	<dd>2.1.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| launchId |
| launchKey |
| body.lis_outcome_service_url |
| body.lis_result_sourcedid |
| result.status | The status or outcome of sending a score to the LTI (See **LTI Status values** below)
| result.dbStatus | Either `recorded` if a record was successfully added to the database, or `error` if not.
| result.launchId | The internal launch id returned
| result.scoreSent | The LTI score (`null` or a value `0` to `1`) sent to an LTI as a replaceResult.
| result.statusDetails | Error information (may have a value of type object, otherwise expect `null`).
| result.ltiAssessmentScoreId | The internal lti assessment score ID
| result.outcomeServiceURL | This is the same as `body.lis_outcome_service_url`
| result.gradebookStatus | See **LTI Gradebook Status values** below

### _lti:pickerLaunch_

Occurs when the LTI document picker was launched from a LMS (Tool Consumer).

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| ltiBody |
| ltiConsumerKey |

## Materia

> These events are exclusive to the Materia OboNode

### _materia:ltiLaunchWidget_

Occurs when a Materia widget is launched in the Obojobo Viewer.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| lisResultSourcedId | Represents a unique use of this widget in the Obojobo Module for the current visit
| resourceLinkId | Represents the unique location of this widget in the Obojobo Module in a course
| endpoint | The URL of the Materia widget (e.g. `https://materia.ucf.edu/embed/XXXXX/widget-name`)

> The `resourceLinkId` uniquely identifies a Materia widget inside an Obojobo Module inside a course. Each play of any given Materia widget will share the same `resourceLinkId`. Note that the `resourceLinkId` will remain the same between different versions of an Obojobo Module, except if the id of the Materia OboNode is changed.

> The `lisResultSourcedId` uniquely identifies the interaction of a Materia widget inside an Obojobo module and course for a given visit. Therefore this ID can be used to find similar events for a given Materia widget inside an Obojobo module for the same visit. This ID will be different for interactions for the same widget across multiple Obojobo modules as well as interactions for the same Obojobo module across different uses (as in, embedded in different courses).

### _materia:ltiPickerLaunch_

Occurs when an author selects a Materia widget to embed in the Obojobo Visual Editor.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| nodeId | The `id` of the Materia OboNode
| endpoint | The URL of the picker UI that is loaded (e.g. `https://materia.ucf.edu/lti/picker`)

### _materia:ltiScorePassback_

Occurs when Materia sends a score back via LTI. Specifically this is fired after Materia sends an LTI `replaceResultRequest` request.

> This event will not be fired in preview mode, and will only be sent for scorable widgets.

> Widgets may allow for more than one play through and may update their score multiple times, so it's possible to see multiple `materia:ltiScorePassback` events for the same Materia OboNode. A student's final highest score for a visit for a Materia node is the `score` value of the last `materia:ltiScorePassback` event for all of those events with the same `lisResultSourcedId`.

> The existence of this event does not indicate a successful replace result. Only events with `success` set to `true` should be considered successful.

<dl>
	<dt>Version</dt>
	<dd>1.0.0</dd>
</dl>

#### Payload

| Property | Description |
|-
| lisResultSourcedId | Represents a unique use of this widget in the Obojobo Module for the current visit
| resourceLinkId | Represents the unique location of this widget in the Obojobo Module in a course
| messageRefId | An internal ID generated by Materia for the corresponding replaceResultRequest request
| messageId | An internal ID generated by Obojobo for the corresponding replaceResultRequest request
| materiaHost | The URL of the instance of Materia that the score originated from (e.g. `https://materia.ucf.edu`)
| score | A number 0-100 representing the student's score of the widget
| success | Boolean - If `false` then the LTI message could not be verified. Otherwise `true`.

> The `resourceLinkId` uniquely identifies a Materia widget inside an Obojobo Module inside a course. Each play of any given Materia widget will share the same `resourceLinkId`. Note that the `resourceLinkId` will remain the same between different versions of an Obojobo Module, except if the id of the Materia OboNode is changed.

> The `lisResultSourcedId` uniquely identifies the interaction of a Materia widget inside an Obojobo module and course for a given visit. Therefore this ID can be used to find similar events for a given Materia widget inside an Obojobo module for the same visit. This ID will be different for interactions for the same widget across multiple Obojobo modules as well as interactions for the same Obojobo module across different uses (as in, embedded in different courses).

## Additional Information

### LTI Status values:

| Description | Description |
|-
| success | The LTI replaceResult was sent successfully
| not_attempted_no_outcome_service_for_launch | The LTI did not have an outcome service so no replaceResult was attempted
| not_attempted_score_is_null | The assessment score was null so no replaceResult was attempted
| not_attempted_preview_mode | User is in preview mode so no replaceResult was attempted
| error_replace_result_failed | Fatal error - The replaceResult failed
| error_no_assessment_score_found | Fatal error - Obojobo was unable to find the assessment score to send.
| error_no_secret_for_key | Fatal error - There was no secret found for the given key
| error_no_launch_found | Fatal error - Obojobo was unable to find the LTI launch details
| error_launch_expired | Fatal error - The LTI launch has expired
| error_score_is_invalid | Fatal error - The score to be sent was not a 0-1 value.
| error_unexpected | Fatal error - An unexpected error occurred.

### LTI Gradebook Status values:

| Description | Description |
|-
| ok_null_score_not_sent | The assessment score was null so no score was sent
| ok_no_outcome_service | The LTI did not have an outcome service so no replaceResult was attempted
| ok_gradebook_matches_assessment_score | Either the new assessment score was sent successfully or the assessment score being sent matches the last assessment score that was sent
| ok_preview_mode | User is in preview mode so no replaceResult was attempted
| error_newer_assessment_score_unsent | A newer score was not sent successfully so the LTI tool consumer does not have the latest score.
| error_state_unknown | Obojobo was not able to determine the history of what was sent to the LTI tool consumer so Obojobo can't determine if the tool consumer should be sent a score or if it has the latest score.
| error_invalid | This is an unexpected state. This state is a fatal error.