---
title: ""
url: https://supabase.com/docs/guides/auth/phone-login?showSMSProvider=Twilio
---

# 

Auth

#

Phone Login

* * *

Phone Login is a method of authentication that allows users to log in to a website or application without using a password. The user authenticates through a one-time password (OTP) sent via a channel (SMS or WhatsApp).

At this time, `WhatsApp` is only supported as a channel for the Twilio and Twilio Verify Providers.

Users can also log in with their phones using Native Mobile Login with the built-in identity provider. For Native Mobile Login with Android and iOS, see the [Social Login guides](/docs/guides/auth/social-login).

Phone OTP login can:

  * Improve the user experience by not requiring users to create and remember a password
  * Increase security by reducing the risk of password-related security breaches
  * Reduce support burden of dealing with password resets and other password-related flows


To keep SMS sending costs under control, make sure you adjust your project's rate limits and [_configure CAPTCHA_](/docs/guides/auth/auth-captcha). See the [_Production Checklist_](/docs/guides/platform/going-into-prod) to learn more.




Some countries have special regulations for services that send SMS messages to users, (e.g India's TRAI DLT regulations). Remember to look up and follow the regulations of countries where you operate.

## Enabling phone login#

Enable phone authentication on the [Auth Providers page](/dashboard/project/_/auth/providers) for hosted Supabase projects.

For self-hosted projects or local development, use the [configuration file](/docs/guides/cli/config#auth.sms.enable_signup). See the configuration variables namespaced under `auth.sms`.

You also need to set up an SMS provider. Each provider has its own configuration. Supported providers include MessageBird, Twilio, Vonage, and TextLocal (community-supported).

### Configuring SMS Providers

##### MessageBird

##### Twilio

##### Vonage

##### Textlocal (Community Supported)

By default, a user can only request an OTP once every 60 seconds and they expire after 1 hour.

## Signing in with phone OTP#

With OTP, a user can sign in without setting a password on their account. They need to verify their phone number each time they sign in.

JavaScriptSwiftKotlinPythonHTTP


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient('https://your-project-id.supabase.co', 'sb_publishable_...')

    4

    5

    // ---cut---

    6

    const { data, error } = await supabase.auth.signInWithOtp({

    7

      phone: '+13334445555',

    8

    })

The user receives an SMS with a 6-digit pin that you must verify within 60 seconds.

## Verifying a phone OTP#

To verify the one-time password (OTP) sent to the user's phone number, call [`verifyOtp()`](/docs/reference/javascript/auth-verifyotp) with the phone number and OTP:

JavaScriptSwiftKotlinPythonHTTP

You should present a form to the user so they can input the 6 digit pin, then send it along with the phone number to `verifyOtp`:


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient('https://your-project-id.supabase.co', 'sb_publishable_...')

    4

    5

    // ---cut---

    6

    const {

    7

      data: { session },

    8

      error,

    9

    } = await supabase.auth.verifyOtp({

    10

      phone: '13334445555',

    11

      token: '123456',

    12

      type: 'sms',

    13

    })

If successful the user will now be logged in and you should receive a valid session like:


    1

    {

    2

      "access_token": "<ACCESS_TOKEN>",

    3

      "token_type": "bearer",

    4

      "expires_in": 3600,

    5

      "refresh_token": "<REFRESH_TOKEN>"

    6

    }

The access token can be sent in the Authorization header as a Bearer token for any CRUD operations on supabase-js. See our guide on [Row Level Security](/docs/guides/auth#row-level-security) for more info on restricting access on a user basis.

## Updating a phone number#

To update a user's phone number, the user must be logged in. Call [`updateUser()`](/docs/reference/javascript/auth-updateuser) with their phone number:

JavaScriptSwiftKotlinPython


    1

    import { createClient } from '@supabase/supabase-js'

    2

    3

    const supabase = createClient('https://your-project-id.supabase.co', 'sb_publishable_...')

    4

    5

    // ---cut---

    6

    const { data, error } = await supabase.auth.updateUser({

    7

      phone: '123456789',

    8

    })

The user receives an SMS with a 6-digit pin that you must verify within 60 seconds. Use the `phone_change` type when calling `verifyOTP` to update a user’s phone number.

### Is this helpful?

No Yes

### AI Tools

Copy as Markdown[Ask ChatGPT](https://chatgpt.com/?hint=search&q=Read from https://supabase.com/docs/guides/auth/phone-login so I can ask questions about its contents)[Ask Claude](https://claude.ai/new?q=Read from https://supabase.com/docs/guides/auth/phone-login so I can ask questions about its contents)