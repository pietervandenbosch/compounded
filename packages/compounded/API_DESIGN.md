Fully mapped to atomic design systems, Compounded should be configurable at all levels. Principles follow AD - and compounds are the functionalities (extra layer on top of AD)

Proposition: it should guide you into going from quark to molecule, to compound eventually.

## quarks
- sizing scale
- typography
- colors
- spacing system
- box shadows
- z-layers
- transition timing functions
- borders
- breakpoints

## atoms
- Label
- Badge
- Button
- Text
- Input
- TextArea
- Toggle
- Checkbox
- RadioGroup
- Slider (form slider)
- List / ListItem
- Icon
- TableCell / TableRow / TableHeading
- Arrow
- Divider (hr)
- Paper
- Sound

## compounds (API only, in the form of hooks)

- Slider (image lists horizontally, vertically,...)
- Table
- DropDown
- AutoComplete
- Form
- Modal
- Grid
- Snackbar / Toast
- DatePicker
- TimePicker
- Tabs
- Interactable (used by buttons etc - interaction abstractions such as hover, click, select,...)
- Tooltip
- Expandable (accordions)
- Media (audio/video player control api)
- Progress
- Image (image loader/display API)
- File (file API)
- Position (useful for position: sticky implementations etc)
- Draggable
- Filter
- Breadcrumbs
- Pagination
- Browser/Device (api abstractions such as react-fns, but in hooks -- geoposition, scrollposition, windowsize, device motion, ... )
- HREF (implementation of e.g. href=mailto or href=sms - but with protection against scraping somehow, if possible)
- Translation/Locale



## molecules
I don't think we need to be concerned with molecules. In userland you would put together all the atoms together with compound api's to generate the molecules

## organisms
In same light as molecules, probably we shouldn't care about this

## API design in userland:

```js
// create quarks with factories

```
