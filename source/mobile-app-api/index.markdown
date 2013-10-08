---
layout: page
title: "Android and iOS 2.0 Mobile App API Documentation"
date: 2013-10-10 09:09
comments: true
sharing: true
footer: true
sidebar: false
show_homepage_banner: true
---

## General Notes

In general, the Canvas mobile app APIs follow the same pattern as the online content in Canvas. Often, simply replacing the `http://` with `canvas-courses://` will take you to the relevant content inside the mobile apps.

If the user is not currently signed into their mobile app, they'll be redirected to a login page for the given domain.

**<i class="canvas-icon-android"></i> Android only:** If the user is already signed into a different domain than the one specified, they'll be redirected to the activity stream and they'll receive a message letting them know they're already signed into a different domain.

**<i class="canvas-icon-apple"></i> iOS only:** If the user is already signed into a different domain than the one specified, an attempt will be made to load the content. If the user has the same email address registered with multiple school accounts, we can often still load the content correctly.

Each API endpoint has an icon showing platform compatability. Android: <i class="canvas-icon-android"></i> and iOS: <i class="canvas-icon-apple"></i>.

_Note: Currently the SpeedGrader app does not support an API._

### Login

Log the user into the given domain, if not already logged in. <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com

### Announcements

Open the app to a list of announcements for a course: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/announcements/
            
Open the app to a specific announcement: <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/courses/:courseID/announcements/:announcementID

### Assignments

Open the app to a specific assignment <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/courses/:courseID/assignments/:assignmentID"

### Conversations

Open the app to the Conversation inbox page page. <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/conversations/

Open the app to a specific conversation <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/conversations/CONVERSATION_ID

### Courses

Open the app to the list of the user's courses: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/

Open the app to a specific course home page: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/

### Discussions

Open the app to a list of discussions for a course: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/discussion_topics/

Open the app to a specific discussion topic <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/courses/:courseID/discussion_topics/:topicID
    canvas-courses://example.instructure.com/groups/:groupID/discussion_topics/:topicID
            
### Files

Open the app to a specific file <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/files/:fileID

### Grades

Open the app to the course grades page: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/grades/

### Pages

Open the app to the list of wiki pages: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/wiki/
    canvas-courses://example.instructure.com/courses/:courseID/pages/
            
Open the app to a specific wiki page: <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/courses/:courseID/pages/:pageName
    canvas-courses://example.instructure.com/courses/:courseID/wiki/:pageName
    canvas-courses://example.instructure.com/groups/:groupID/pages/:pageName
    canvas-courses://example.instructure.com/groups/:groupID/wiki/:pageName
            
### People

Open the app to the list of people in a course: <i class="canvas-icon-android"></i>

    canvas-courses://example.instructure.com/courses/:courseID/people/
    canvas-courses://example.instructure.com/courses/:courseID/users/

### Quizzes

Open a specific quiz. <i class="canvas-icon-android"></i> <i class="canvas-icon-apple"></i>

    canvas-courses://example.instructure.com/courses/:courseID/quizzes/:quizID

<div style="font-size: 12px; margin-top: 35px; border-top: 1px solid #d0d0d0;">
  Last Updated: 28 Jun 2013.
  <a href="https://github.com/instructure/instructure.github.com">Code</a> licensed under the MIT License. Documentation licensed under CC BY 3.0.
</small>