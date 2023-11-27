---
title: Helpful JavaScript Functions for Shiny
author: Kent Russell
date: '2023-11-26'
slug: shiny-helpful-js
categories: []
tags: []
---

Over the years consulting on Shiny projects, two JavaScript functions have been very helpful to me and serve a core role in applications.  Thanks so much to the project sponsor who has generously allowed me to share this code.

## `isInputChanged`
On the `R` side, observers and reactives are only triggered when an input changes.  However JavaScript's `shiny:inputchanged` often will trigger when an input updates even if nothing changes.  `isInputChanged` lets us know if the input changed.  This means we can ignore unchanged inputs in JavaScript, especially for expensive or cumbersome operations.

```{js}
// an imperfect function to see if Shiny input has changed
function isInputChanged(evt) {
  if(Shiny && Shiny.hasOwnProperty("shinyapp") && Shiny.shinyapp.hasOwnProperty("$inputValues")) {
    var oldvalue = null;

    for(var ky in Shiny.shinyapp.$inputValues) { // use for to only find first match and exit early
      if(ky.replace(/(.*)(:.*)/,"$1") === evt.name) { // handle :binding in name
        oldvalue = Shiny.shinyapp.$inputValues[ky];
        break;
      }
    }
    return evt.value !== oldvalue;
  } else {
    return false;
  }
}
```

## `cancelInput`
Sometimes, we will want to validate a user's change before setting off the waterfall of reactive dependencies or doing consequential things.  For instance, we might want to show a modal asking the user for confirmation before overwriting data.  Often, I pair `cancelInput` with `isInputChanged`.

```{js}
// similar to changedInput to cancel a shiny input change
//   by restoring its original value
function cancelInput(evt) {
  if(Shiny && Shiny.hasOwnProperty("shinyapp") && Shiny.shinyapp.hasOwnProperty("$inputValues")) {
    var oldvalue = null;
    var newvalue = evt.value;

    for(var ky in Shiny.shinyapp.$inputValues) { // use for to only find first match and exit early
      if(ky.replace(/(.*)(:.*)/,"$1") === evt.name) { // handle :binding in name
        oldvalue = Shiny.shinyapp.$inputValues[ky];
        break;
      }
    }
    if(evt.value !== oldvalue) {
      evt.value = oldvalue;
    }
  }
  // cancel event
  evt.preventDefault();
  return {
    event: evt,
    oldvalue: oldvalue,
    newvalue: newvalue
  };
}
```

## Example Usage

Altogether now, let's see how this might look in a simple contrived app in which a user selects a letter.  When the user selects a different letter, we confirm the change with a modal in which the user can accept or reject their own change.  The code is below, or through the miraculous `shinylive` we can see a live example in the browser [shinylive example](https://shinylive.io/r/editor/#code=NobwRAdghgtgpmAXGKAHVA6ASmANGAYwHsIAXOMpMAGwEsAjAJykYE8AKAZwAtaJWAlAB0IdJiw71OY4RBEBXWgAIAPAFolqKAHM4AfQBm1RQBN2IpUs5xqcAqVoAvOAEkIqeaXMRLlvh9IXEyUAXiUAcltSckZrW3siRnDcC19qKHobUKUhMAAFWgIAayUoJQAZOGi4RlzcHJ9fAm4iQrhObKiYzmAARkQAFgBdFMbLOLtyYLCump7eodSBUcsAYiUAR0VipTpo2yUYIhMoalKT1CmlA0YiGCVuaNRORAB6V91SeiIiUk5SZiYYgwV4mIgETivACsGAATK9gagSBQ-q8jidqK9UgAJAAqAFlyt5wiIVCZaAA3JQEdKcTghXLo065JSkDJ8ExwAAeDLAal6uQAfKkyZTqbT6Yzjqc1OTTkRtELUpZRVSaVA6bymdQ1MQyCilWNfKrxRrJWBtWpuHAoJzamBhUbfKpuFDTZqpRi1A5SLYhQBhbhQCC6YKVaqMJQAZRsk1oJBUr1djudKtBlJTqZN6o9FulOu+JlYhtTxtQgoAgow4EpWER5FZ5NWlAB3Gs8evUYLNYO6VnW3ZVGIAfkT5eVxvTFMzzuzEq1+bUBh+MRLpdU9E8pBIrNYqDgvM30RILJz5vopB8F4gamsepObCU17UECILJObLUUlltE4MF-5rakKAByRCJke24QDOWYQTupB7geuSwXIYDuuel5PpeaioIwtAwBImE3qw7RCgAmu04FbiQ0FpuS04TomdGZoxGaklOjrhM6SwrEo6xbIUJR7L6NbaucaBXDcdwPE8LzvJ83y-P8gIYMCoLgpCMLwoiyJkJC2pYo0bLaJwAAk1q2uwRmmZwBC4Zc7B4oSxIiO8pQ+Hh+6MAYkzXPIED2PGPjblYcA1rQBjRrw-BKP4ngPBq1JBiGcAmCIBh+QFO6-m4ASBr2KXsHAFKkAISggKk4XsFGUWsEoABkdWRXwrAYEGnAAPIthAeS3J58HmGAPDNWgqC5KVDVNfwGBDfwI2tRqnXdb1NT9bkJmxaQABqpzyCRYACKV5VGhSLBKEQXYncYNZhBA8jUNQADcIgTsujDsCdkZFLVfCTS1M2sHN63uJ421XZwh1KK58jWNciSskQZ2iLVBgctctCxKQhxQKQzRucE3K0JjNqMNQxZOjFBjsF9GDVqg6QEHA7CvOwGAAFQCOwiBswIrwpGAJkCvtoQhGERWkBg0DwBDrlJSYByIPQHJ8NoMU+JLcATs650mJdu3ZNVzXTTVgMbaDu09F9QxPeTlhMDaRTW6WAC+E4u0a1akE2Phixgus1gAhCLZ0XTtcCO0oTtKDYMNHc6Hte9cpzWOHbtuy5rxWHhtDpJGwU9slJg5XFefBgzZxlP9qsBIl+Xp749C1dW-yJMrMV-GduHaHwpxKH7aUZQ4O4EKXNhF14YuHRVlMG9FE0zy1bWLT1RB9RwuT-SNY31Y189G8N6DzR1XXL6vA1AwEZt7QdZUTh9wc66H2S3fd4eWHfEBwC2fvZD7fvWy9iR3qnS+qrX6e9ZoH3PiDUO4MyqQwztDGsr14aI1JmjCAwQUYYyxjjbgeMo5ckJlHFgpNNYUypi1Wm9NGbM25pzbmvM1qC1KiLUWxUJawDgNLDOst5aKwwa3H66syGWG1t-MIu8N6QNNjA4AltX6pjtlAB2ZC3bOjUb4Sqv9H6BzCGI0Ok9ybaKutkfRV0FEaI0a5Ye-kshFQNI0H2OF7FkAACJwAMFAO6XgBDh3jowHwsdfAuNIIgKOxUeKiJDldMJZjdqRKUB-L+ocwlJL7o0J21s3acV8EsA6PF1iIKUAAKSjL5fyg8IAdDziQLB9xEGRnzn2FshM8FAUMjoaytlaD2UckSEQJIIAmXYGCAg8h4BkAEBgEgA1-qIA2k0lKdRymZQgIVYqhi-CUx9urYWYRcizFiLGBI9pt4xU4GPPKBd1klU2b4dg6UKmBXYHc501iR5nA2pnWxrYawtmDJjPUAJzpPg1ClRGSgGmJVaAzMhrklD+J8N-Fp90nw1mDGdegAArHyLTSB4JCfUFuXdoBnD9vUYMwQkm91DmQu+hzsg2LLmPG5viyFwozh2Fshx8zUlqejf8IZWxBkxlClsnZgh0CKDWYKRAKQ1BbLhcgZC9T-EOKwfEvKbqfyfCuJSaAMCaoxOwYZuQMBAX2myuQ5M77an9B87ElLbCRjCI81ZLyb423gRTakHzbDBHxRQX5rZs5nA-uC4K1hMaBpCvEBwzhQU7GCi2a0zYiH-M4CI71lgY1fI7HdYIpxqy2lqvhXB-YazaxpSYzgfAGbBqZTYf1WbTVgFWIciYJyxrAAAAxDGmsc+NcAB1bVDuwQ50zom7Steub1NIbQ+HkKgUoBgYhnSbHEeVHRKUIrgEceV8UMHOszV61t5reUYGfK+Ma0yDCU1yDSASyy7UOqdTUGd64z2iUvVhYimb9q3vvYQOgxRn35n9PyxgMBHVHvfQo50MANX5larQTkLz4MR1SHS06drIPQbfS6lZlSPVBNLK5cKpQCAM0uBG60Pg2whtRa+bl1gMFV2LgjLAD0QqBGBqOkxYJ2iJN+KyTuuhIxzIWUlUMz0vVRtxHhOA9YvBZssG64jrzZ3Zu+fWhj4aA0I2BDAPyhRsY1m-pGmqqnfDwvCeLcRiTP7f3gPi44jGw0ifFYwEotafnQ1bsPe69AoDFBPVpmznKKABoHB+Mo5Jqz2DQcFeeIb8UhQwWPazlhbMpsKG0hdHRJN8cWfjeVZB3PCcxpkUTtBtDiaWV6t5GdnTOLKw4IVGK+AowgITGs1AfijUa74SRxsD6scLnx9g5UaBDjmIOxIuQwkTrSaHJ2H7Z1OwSaWHtWb1upizdY2wGKl0rrXfWI51At34OrPumsvC5gtoGuejESgf03mvQBogd6BqPtA3gHlGJ7W2OoDBuWcHHtmu-c+P9N6vtAd+0UMDgO8Og+dXthDSGMQobQ+jzDjQyFfovVet8n21kPpA4j-7L7geo-B+TQnL23tqBh6Tn7FOkenAgxAOptPGAfrIYho1pwjZEBbOh12HNZCWFTrIQZlgDoiFkCIawjB5WRnUER55G0iWeACPUawdJAp3KIFIGo8qACibX2AbRMh2+bjB6ikc0LhMg7AtD-DgD2gaDKSsoLqDbu3cbEgK4yaVBoacqk1QrOgdgihsiKH12bmoYQVdq4EGAJ2QwgA).

```{r]
library(shiny)
library(bslib)

ui <- page_fluid(
  selectizeInput(
    inputId = 'letterselector',
    label = "Pick a Letter", 
    choices = letters[1:4],
    selected = letters[1]
  ),
  # quick little modal adapted from https://getbootstrap.com/docs/5.2/components/modal/
  HTML(
'
<div class="modal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Changed Letter Selection</h5>
      </div>
      <div class="modal-body">
        <p>Are you sure we should change the letter?</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary btn-no" data-bs-dismiss="modal">No</button>
        <button type="button" class="btn btn-primary btn-yes">Yes</button>
      </div>
    </div>
  </div>
</div>
'    
  ),
  # quick little modal adapted from https://getbootstrap.com/docs/5.2/components/modal/
  tags$head(tags$script(HTML(
'
// an imperfect function to see if Shiny input has changed
function isInputChanged(evt) {
  if(Shiny && Shiny.hasOwnProperty("shinyapp") && Shiny.shinyapp.hasOwnProperty("$inputValues")) {
    var oldvalue = null;

    for(var ky in Shiny.shinyapp.$inputValues) { // use for to only find first match and exit early
      if(ky.replace(/(.*)(:.*)/,"$1") === evt.name) { // handle :binding in name
        oldvalue = Shiny.shinyapp.$inputValues[ky];
        break;
      }
    }
    return evt.value !== oldvalue;
  } else {
    return false;
  }
}

// similar to changedInput to cancel a shiny input change
//   by restoring its original value
function cancelInput(evt) {
  if(Shiny && Shiny.hasOwnProperty("shinyapp") && Shiny.shinyapp.hasOwnProperty("$inputValues")) {
    var oldvalue = null;
    var newvalue = evt.value;

    for(var ky in Shiny.shinyapp.$inputValues) { // use for to only find first match and exit early
      if(ky.replace(/(.*)(:.*)/,"$1") === evt.name) { // handle :binding in name
        oldvalue = Shiny.shinyapp.$inputValues[ky];
        break;
      }
    }
    if(evt.value !== oldvalue) {
      evt.value = oldvalue;
    }
  }
  // cancel event
  evt.preventDefault();
  return {
    event: evt,
    oldvalue: oldvalue,
    newvalue: newvalue
  };
}
'    
  ))),
  # use JS functions to confirm user change with modal
  tags$script(HTML(
'
$(document).on("shiny:inputchanged", function(evt) {
  if(evt.name === "letterselector" && isInputChanged(evt)) {
    (function() {
      // cancel input since we want control based on user choice
      //  return value will be an object with event, original value, and new value
      var letter = cancelInput(evt);
      
      // show modal confirming what user would like to overwrite
      const myModal = new bootstrap.Modal($(".modal"));

      var modalCancelHandler = function() {
        // if cancelled then we will need to set the selectize back to where it was
        //   the input should already match the old value since we cancelled
        $("#letterselector")[0].selectize.setValue(letter.oldvalue);
        // clean up after ourselves and remove handlers
        $(".modal .btn-no").off("click", modalCancelHandler);
        $(".modal .btn-yes").off("click", modalConfirmHandler);
        myModal.hide();
      }
  
      var modalConfirmHandler = function() {
        // if accepted then we will now send input to R; setInputValue does not trigger shiny:inputchanged

        setTimeout(
          function() {
            //   since we need to communicate value to shiny
            //   evt.value = newvalue method will not work since using callbacks
            // send the data directly to Shiny with sendInput
            //   which means shiny:inputchanged event will not be triggered
            //     preventing an infinite loop
            Shiny.shinyapp.sendInput({"letterselector": letter.newvalue});
          },
          0
        );
        
        // clean up after ourselves and remove handlers
        $(".modal .btn-no").off("click", modalCancelHandler);
        $(".modal .btn-yes").off("click", modalConfirmHandler);
        myModal.hide();
      }
  
      $(".modal .btn-no").on("click", modalCancelHandler);
      $(".modal .btn-yes").on("click", modalConfirmHandler);
  
      myModal.show();
    })()
  }
})
'
  ))
)

server <- function(input, output, session) {
  observeEvent(input$letterselector, {
    print(paste0("letter changed to ",input$letterselector))
  })  
}

shinyApp(ui = ui, server= server)
```
