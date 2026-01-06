# Model Picker Panel

A powerful FiftyOne plugin that provides an interactive panel for managing model predictions in your dataset. Selectively display fields, analyze statistics, and save presets for quick access.

## Features

### 📋 Select Fields Tab
![](https://github.com/prernadh/model_picker/blob/main/select_fields_gif.gif)
- 🎯 **Interactive Selection**: Check/uncheck fields with real-time search
- 📊 **Smart Grouping**: Organize by Level (Sample/Frame), Type (Detection/Classification), or Model Evaluations
- ✅ **Bulk Controls**: Select All / Deselect All buttons
- 📝 **Field Notes**: Add documentation to any field (persisted in dataset metadata)
- 🎨 **Evaluation Badges**: Visual indicators showing prediction vs ground truth fields
- 🔄 **Collapsible Groups**: Organize large field lists efficiently
- ⚡ **Instant Feedback**: See selection count before applying

### 📊 Field Statistics Tab
![](https://github.com/prernadh/model_picker/blob/main/field_statistics_gif.gif)
- 📈 **Label Counts**: Total number of labels per field
- 🏷️ **Class Distribution**: View all classes with counts
- 📊 **Same Grouping**: Group by Level, Type, or Evaluations
- 📝 **Documented**: Add notes directly from statistics view

### 💾 Saved Views Tab
![](https://github.com/prernadh/model_picker/blob/main/saved_views_gif.gif)
- 💾 **Save Presets**: Store field selections for reuse
- 📂 **Quick Load**: Restore saved configurations instantly
- 🗑️ **Easy Cleanup**: Delete outdated presets
- 🎯 **Comparison Workflows**: Create presets for common comparison scenarios
- ✨ **Visual Feedback**: Active view highlighted in green, newly created in orange

## Installation

fiftyone plugins download https://github.com/prernadh/model_picker

## Usage

### Opening the Panel

1. **Load a dataset** in FiftyOne App
2. **Open the Panels menu** (Click the `+` icon next to `Samples`)
3. **Select "Model Picker"** from the available panels
4. The panel will appear in the sidebar

### Select Fields Tab

1. **Search**: Type in the search box to filter fields by name
2. **Select/Deselect**: Toggle checkboxes to choose which fields to display
3. **Group By**: Use the dropdown to organize fields:
   - **Level**: Sample-level vs Frame-level fields (video datasets)
   - **Type**: Detection, Classification, Polylines, etc.
   - **Model Evaluations**: Group by evaluation runs (shows prediction/ground truth roles)
4. **Add Notes**: Click the notes textarea under any field to add documentation
5. **Apply Selection**: Click "Apply Selection" to update the view
6. **Save View**: Click "Save Current View" to create a reusable preset

### Field Statistics Tab

1. Switch to the **Field Statistics** tab
2. View **label counts** and **class distributions** for each field
3. Use the same **grouping options** as Select Fields
4. Add **notes** for documentation purposes

### Saved Views Tab

1. Switch to the **Saved Views** tab
2. **Load** a saved preset by clicking "Load View"
3. **Delete** outdated presets with the "Delete" button
4. The **active view** is highlighted in green
5. **Newly created** views are highlighted in orange (until you navigate away)

## Architecture

### Data Flow

```
Panel Opens → Python: GetLabelFieldsMetadata → React: Display fields
                                                       ↓
User Selects/Searches → React: Local State Updates (instant)
                                                       ↓
Click "Apply" → Python: ApplyModelPicker → View Updates
                                                       ↓
Click "Save View" → Python: SaveModelPickerView → Saved to dataset
```

### Hybrid Design

- **Python**: Handles all data operations (schema introspection, statistics, view filtering, persistence)
- **React**: Provides rich interactive UI (search, grouping, real-time state management)
- **Clear Separation**: Business logic in Python, presentation in React

## Python Operators

### Public Operator

- `model_picker` - Legacy checkbox dialog (kept for backwards compatibility)

### Internal Operators (unlisted)

1. `get_label_fields_metadata` - Fetch field schema with evaluations and notes
2. `get_label_fields_statistics` - Compute label counts and class distributions
3. `apply_model_picker` - Filter view to show only selected fields
4. `update_field_notes` - Save notes for a specific field
5. `save_model_picker_view` - Save current view as a preset with [Model Picker] prefix
6. `list_model_picker_views` - List all saved Model Picker presets
7. `delete_model_picker_view` - Delete a saved preset

## File Structure

```
model_picker/
├── __init__.py                 # 8 Python operators (~600 lines)
├── fiftyone.yml                # Plugin metadata
├── README.md                   # This file
├── package.json                # React dependencies
├── vite.config.ts              # Build configuration
├── tsconfig.json               # TypeScript config
├── src/
│   ├── index.ts                # Panel registration
│   └── ModelPickerPanel.tsx   # Main panel component (~900 lines)
└── dist/
    └── index.umd.js            # Compiled bundle (~35 kB)
```

## Technical Details

### JavaScript Dependencies

- React 18.2.0
- Recoil (for FiftyOne state management)
- @fiftyone/operators, @fiftyone/state, @fiftyone/components
- @mui/material (for theming)

### Python Dependencies

- FiftyOne SDK
- Standard library (collections.defaultdict)

### Key Technologies

- **TypeScript** for type safety
- **React Hooks** (useState, useEffect, useMemo, useCallback)
- **FiftyOne Operator System** for Python ↔ JavaScript communication
- **Dataset Metadata** for notes storage
- **Saved Views API** for preset management

## Development

### Build the Plugin

```bash
cd /path/to/fiftyone-plugins
FIFTYONE_DIR=/path/to/fiftyone yarn workspace @prernadh/model_picker build
```

### Development Mode

```bash
yarn workspace @prernadh/model_picker dev
```

**Build Stats:**
- Build time: ~300ms
- Bundle size: ~35 kB (gzip: ~10.7 kB)

## Advanced Features

### Notes System

- Add documentation to any field to help team members understand its purpose
- Stored in `dataset.info['model_picker_field_notes']`
- Persists across sessions and is dataset-specific
- Visible in both Select Fields and Statistics tabs
- Supports markdown-style formatting

### Evaluation Mapping

- Automatically detects evaluation results associated with label fields
- Shows which fields are predictions vs ground truth with colored badges
- When hiding a field, automatically hides associated evaluation fields
- "Group by Evaluations" mode shows all fields related to each evaluation run
- Ground truth fields listed first, then prediction fields

### Saved Views

- Stores view name, optional description, and optional color
- Automatically prefixes description with "[Model Picker]" to identify plugin-created views
- Only displays Model Picker views in the Saved Views tab (filters out others)
- Uses FiftyOne's built-in saved views system for reliability
- Views are dataset-specific and persist across sessions

## Use Cases

1. **Quick Model Comparison**: Create presets like "Best 3 Models" or "Latest vs Baseline"
2. **Team Collaboration**: Use notes to document model versions, training dates, or known issues
3. **Presentation Mode**: Load a "demo" preset showing only your best-performing models
4. **Development Workflow**: Create presets for different stages (training, validation, production)
5. **Evaluation Analysis**: Group by evaluations to see predictions and ground truth side-by-side
6. **Documentation**: Add notes explaining what each model does and when to use it

## Troubleshooting

### Panel doesn't appear

- Verify FiftyOne plugins are enabled in your settings
- Run `fiftyone plugins list` to check installation
- Rebuild the plugin: 
```bash
cd /path/to/fiftyone-plugins
FIFTYONE_DIR=/path/to/fiftyone yarn workspace @prernadh/model_picker build
```
- Restart the FiftyOne App

### Statistics don't load

- Large datasets may take a few seconds to compute statistics
- Check browser console (F12) for errors
- Ensure you have the latest version of FiftyOne

### Saved views don't persist or Saved views show incorrect selections

- Views are dataset-specific - make sure you're in the correct dataset
- Ensure that you are pressing `Apply selection` before the
  `Save Current View` button 

### Notes disappear

- Notes are stored in `dataset.info` metadata
- Saving happens automatically via the `update_field_notes` operator
- If issues persist, try accessing dataset info through the SDK, edit it and
  save with `dataset.save()`

### Apply Selection doesn't work

- Ensure at least one field is selected
- Check that the fields you selected are valid label fields
- Try refreshing the panel by switching tabs

## Example Workflows

### Workflow 1: Model Comparison

1. Open Model Picker panel
2. Deselect all fields
3. Select only the 2-3 models you want to compare
4. Click "Apply Selection"
5. Click "Save Current View" and name it "Comparison: ModelA vs ModelB"
6. Next time, just load this saved view from the Saved Views tab

### Workflow 2: Documentation

1. Switch to Field Statistics tab
2. Review each field's label counts and classes
3. Add notes explaining:
   - Model version and training date
   - Known issues or limitations
   - When to use this model
4. Notes are saved automatically and visible to your team

### Workflow 3: Evaluation Analysis

1. In Select Fields tab, group by "Model Evaluations"
2. You'll see fields grouped by their evaluation runs
3. Prediction and Ground Truth fields are shown together
4. Add notes to document evaluation results
5. Save as a preset for quick access during review meetings

## Version

**1.0.0** - Full-featured release with 3-tab interface

## License

Apache 2.0

## Author

@prernadh


## Changelog

### 1.0.0 (Current)

- ✨ Interactive JavaScript panel with 3 tabs
- 📊 Field Statistics with label counts and class distributions
- 💾 Saved Views for reusable presets
- 📝 Notes system for field documentation
- 🎨 Evaluation role badges (prediction/ground truth)
- 📋 Group by Level, Type, or Model Evaluations
- 🔍 Real-time search functionality
- ✅ Select All / Deselect All controls
- 🎯 Optimistic UI updates with triggerPanelEvent
- 🗑️ Delete saved views with immediate UI feedback

### 0.1.0 (Legacy)

- Basic Python checkbox dialog
- Simple field selection
- No statistics or saved views
