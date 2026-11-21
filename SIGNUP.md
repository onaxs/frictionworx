# Interest form → EmailOctopus

The site's signup dialog ("Newsletter", "I'm interested", and the FAQ links) posts directly to
EmailOctopus form `f6692ce0-e5e1-11f1-b22d-2959b78bdf53` (a pop-up form in EmailOctopus).
The site uses its own form design and only borrows that form's address, so **don't paste the
EmailOctopus embed code into the site**, or EmailOctopus's own pop-up will appear as well.
reCAPTCHA and the anti-spam honeypot work the same way as in the embed.

## How the fields are set up

On the mailing list's **Fields** tab:

| Field     | Type                  | Choices (number = order)                                              |
|-----------|-----------------------|-----------------------------------------------------------------------|
| Role      | Choice (select one)   | 1 Wheelchair user, 2 Clinician or ATP, 3 Caregiver or helper, 4 Other |
| Interests | Choice (select many)  | 1 Newsletter, 2 Prototype testing and feedback                        |
| Note      | Text                  |                                                                       |

All three are on the form in Grow → Forms. First name and Last name are also on the form;
the site doesn't send them, which is fine.

The site sends:

| Site value               | Form field   | Sent as                                         |
|--------------------------|--------------|-------------------------------------------------|
| Email                    | `field_0`    | the address                                     |
| Role                     | `field_6`    | the choice number (1–4)                         |
| Newsletter / prototype   | `field_7[]`  | 1 for newsletter, 2 for prototype, one entry each |
| Note                     | `field_8`    | the text                                        |

Choice fields are named by their **column number** in EmailOctopus, not their position on the
form, and they take the choice's **number**, not its label. These are set in `SIGNUP` near the
top of the `<script>` in `index.html` (`fields`, `roles[].id`, `interests`).

## Checking the field numbers

If you add, remove or recreate fields, the numbers can change. To check them, download
`https://eocampaign1.com/form/f6692ce0-e5e1-11f1-b22d-2959b78bdf53.js`. The form's markup is
stored inside it as base64. Decode it and look for:

- text and email fields: `name="field_N"`
- choice fields: `data-column-index="N"` (name is `field_N`, plus `[]` for select-many),
  with the choice numbers in `data-choices`

If anything differs from the table above, update `SIGNUP` in `index.html` to match.

## Testing

Test on the live site, not a local copy (reCAPTCHA and EmailOctopus may refuse other domains).
Submit once with both boxes ticked and once with neither, then check the two contacts in
EmailOctopus. If you see "Sorry, something went wrong", open the browser console and look for
`Signup failed: <code>`. `INVALID_PARAMETERS` means a field name or choice number doesn't match.

## Using the data

- **Newsletter:** everyone who submits is added to the list, but only people who ticked
  "Send me the newsletter" have Newsletter in Interests. Create a segment
  *Interests contains Newsletter* and send newsletters to that segment, not the whole list.
- **Roles:** create a segment per role, e.g. *Role is Clinician or ATP*, to email each group.

## Changing the choices

1. Add or rename the choice in EmailOctopus (list → Fields).
2. Check its number (see above).
3. In `index.html`, add or edit the matching line in `SIGNUP.roles` (with its `id`) or `SIGNUP.interests`.
