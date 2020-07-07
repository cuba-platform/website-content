## Overview

Sneferu is a testing library for simplifying web integration testing for a CUBA application. It consists of a couple of APIs that allow you to express interactions and verifications with UI screens via a dedicated high-level testing language. Sneferu is the way to have very good test coverage and quality assurance at a fraction of the cost.

Via its easy-to-read language, you can create integration tests that are optimized for readability because this is what matters most for keeping a test suite maintainable & cheap to operate.

## Features

With Sneferu it is possible to:

- Verify any business logic in a Screen Controller
- Ensure the correct linking between Screen XML and its Controller counterpart
- Verify correct display of any programmatic creation of Screen Components / Dialogs
- Emulate & verify data loading
- Emulate & verify Service Interface interactions

What Sneferu *does not cover*:

- End-to-End data loading and displaying on a Screen
- End-to-End Service execution (middleware)
- Client-Side Vaadin UI logic that is executed only in the browser

## Links

- [Documentation](https://github.com/mariodavid/sneferu/blob/master/README.md)
- [Sample project](https://github.com/mariodavid/cuba-petclinic-using-sneferu)