# Interest form → EmailOctopus

The “I’m interested” dialog posts directly to the existing EmailOctopus form
(`8f7d4b80-48fa-11f1-af56-3744e15c4d6e`), so signups land in the same list as before.
The site's own form replaces EmailOctopus's embed, so the styling is ours. reCAPTCHA and the honeypot work the same way as in the embed.

## One-time setup in EmailOctopus

The page sends three extra fields. They must exist on the form, or EmailOctopus
rejects the submission with `INVALID_PARAMETERS`.

1. **Create the fields on the list.** Open the mailing list the form feeds, go to the
   **Fields** tab, and click **Add field** for each:
   - Label **Role**, Type **Choice (select one)**. Choices, spelled exactly:
     `Wheelchair user`, `Clinician or ATP`, `Caregiver or helper`, `Other`
   - Label **Interests**, Type **Choice (select many)**. Two choices: `Newsletter`, `Prototype testing`
   - Label **Note**, Type **Text**
2. **Put them on the form.** Go to **Grow → Forms** and open the existing form. Click
   the sign-up form, then add Role, Interests and Note from the field menu on the left,
   **in that order**, below the email field. Leave them as regular fields, not hidden,
   and don't mark them required. Save or publish the form.
   The site uses its own form, so the look of the EmailOctopus form doesn't matter.
3. Confirm the field numbers: open
   `https://eocampaign1.com/form/8f7d4b80-48fa-11f1-af56-3744e15c4d6e.js`. Email is
   `field_0`; the new fields should be `field_1`, `field_2`, `field_3`. If they differ,
   update `SIGNUP.fields` near the top of the `<script>` in `index.html`.
   A multiple-choice field keeps its `[]` suffix (`field_2[]`).
4. Send one real test signup from the live page and check that the contact shows its Role.

## Using the categories

- **Newsletter:** everyone who submits the form is added to the list, but only people who ticked
  “Send me the newsletter” have `Newsletter` in Interests. Create a segment *Interests contains Newsletter*
  and send newsletters to that segment, not the whole list.
- **Segments:** create one per role, e.g. *Role is Clinician or ATP*, to email each group separately.
- Role shows up as a column on each contact and in CSV exports.

## Changing the categories

Edit the radio `value`s in `index.html` **and** the choices in EmailOctopus. The two must match exactly.
To let people pick more than one role, change the Role field to multiple choice, set
`role: 'field_1[]'`, switch the inputs to checkboxes, and append each checked value.
