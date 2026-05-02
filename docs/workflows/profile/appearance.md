# Appearance Workflow

## Rules

Appearance preferences can be updated by the signed-in user.

Valid colour modes are:
1. `light`
2. `auto`
3. `dark`

Valid themes are:
1. `default`
2. `mustafar`
3. `endor`
4. `hoth`
5. `dagobah`
6. `coruscant`
7. `tatooine`

Legacy saved value `imperial` is accepted for compatibility and displayed as `mustafar`.

Legacy saved value `rebel` is accepted for compatibility and displayed as `endor`.

Legacy saved value `jedi` is accepted for compatibility and displayed as `hoth`.

Legacy saved value `crawl` is accepted for compatibility and displayed as `dagobah`.

Profile updates must upsert the profile row when one does not already exist.

## Client Side Validation

Client side validation occurs when a colour mode or theme option is selected.

On colour mode selection:
1. If selected colour mode is already active, do nothing
2. If a colour mode save is in progress, ignore additional selections
3. Otherwise call update profile API with selected colour mode

On theme selection:
1. If selected theme is already active, do nothing
2. If a theme save is in progress, ignore additional selections
3. Otherwise call update profile API with selected theme

## Server Side Validation

Server side validation must occur at the API level.

The update profile API must validate:
1. Caller is authenticated
2. Colour mode is one of the valid colour modes when provided
3. Theme is one of the valid themes when provided

If colour mode is invalid, return bad request.

If theme is invalid, return bad request.

## Colour Mode Workflow

1. User opens Profile page
2. User selects Light, Auto, or Dark
3. Client calls update profile API with colour mode
4. API validates JWT
5. API validates colour mode
6. API creates profile row if needed
7. API updates colour mode
8. API returns updated profile
9. Client updates colour mode context
10. Client displays success message

## Theme Workflow

1. User opens Profile page
2. User selects a theme
3. Client calls update profile API with theme
4. API validates JWT
5. API validates theme
6. API creates profile row if needed
7. API updates theme
8. API returns updated profile
9. Client updates theme context
10. Client displays success message

## Failure Workflow

1. If colour mode save fails, display failed-to-save colour mode message
2. If theme save fails, display failed-to-save theme preference message
3. If network fails, display network error message
4. Keep the user on the Profile page
