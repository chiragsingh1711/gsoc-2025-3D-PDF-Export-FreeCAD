# GSoC'25 Final Report - 3D PDF Export in FreeCAD

My GSoC'25 Submission for 3D PDF Export Implementation in FreeCAD

## Project Overview

The 3D PDF Export for FreeCAD project aimed to develop a native capability for exporting interactive 3D models directly to PDF format, enhancing FreeCAD's documentation and collaboration capabilities.

Earlier, FreeCAD users relied on complex multi-step workflows involving external tools like MeshLab, LaTeX, or even commercial CAD software to create 3D PDFs. This often resulted in loss of model fidelity, colors, and structure, making it difficult for users to share their work with clients and collaborators who don't have CAD software.

This project implements a seamless 3D PDF export feature integrated directly into FreeCAD's TechDraw workbench. Users can now create standard technical drawings and add 3D Active Views that will be exported as interactive 3D embeds in the PDF. This means anyone can view and interact with the 3D model using just a PDF reader like Adobe Reader.

[Proposal PDF](images/pdfs/Issue-12843%20%20Proposal%20-%20Chirag%20Singh.pdf?raw=true)

[FreeCAD Forum Topic](https://forum.freecad.org/viewtopic.php?t=95828&sid=dd62e37b05f3bf725c45470ccbab37a3)

## Project Goals

### 1. U3D Export Implementation

The first goal was to implement a direct U3D format exporter for selected objects from FreeCAD. This would allow users to export their 3D models in a format that can be embedded in PDF files without needing any external tools.

### 2. PRC Export Implementation

The second goal was to implement a direct PRC format exporter for selected objects from FreeCAD. PRC format is better than U3D because it preserves more details of CAD models and creates smaller file sizes.

### 3. Global 3D PDF Export

The third goal was to create a global 3D PDF export feature that would let users export any selected objects from their FreeCAD document into a PDF with embedded 3D content. This gives users a simple way to share their models with others.

### 4. TechDraw Integration

The final goal was to integrate this 3D PDF export with FreeCAD's TechDraw workbench. This integration would allow users to create professional technical drawings that combine traditional 2D views with interactive 3D elements, all in one PDF document.

## Accomplishments

### 1. Exported both U3D and PRC files

The first major accomplishment was implementing both U3D and PRC export methods. The goal was to try both formats and identify which one works better for FreeCAD models. After testing both formats with various FreeCAD models, I created a detailed comparison to understand their differences.

Following table is a detailed comparison between U3D and PRC:

| Feature           | PRC              | U3D      | Importance for FreeCAD                     |
| ----------------- | ---------------- | -------- | ------------------------------------------ |
| NURBS Support     | Yes              | No       | Critical for preserving exact CAD geometry |
| File Size         | Smaller          | Larger   | Important for complex models               |
| CAD Data Fidelity | High             | Low      | Essential for engineering use cases        |
| Compression       | Better           | Basic    | Helps with large assemblies                |
| B-rep Support     | Yes              | No       | Maintains precise edges and surfaces       |
| Color/Material    | Per face         | Limited  | Preserves design intent                    |
| Standard          | ISO 14739-1:2014 | ECMA-363 | Industry adoption                          |

This comparison brought us to the conclusion of focusing only on PRC implementation for the final version. PRC format preserves the exact geometry of CAD models and creates smaller files, which is exactly what FreeCAD users need.

### 2. Integrated a Global 3D PDF Button on the Top Level Menu Bar

I added a new button to FreeCAD's main menu that lets users export 3D PDFs easily. This button would let users export a default 3D PDF containing a 3D embed for all the selected objects in the document. When users select objects in their document and click this button, it creates a PDF file with interactive 3D content that can be viewed in any PDF reader.

### 3. TechDraw Implementation Phase 1

To implement 3D PDF export from TechDraw, I first needed to create a way for users to specify which parts of their drawing should become 3D embeds. Implementing a completely new placeholder would mean a huge task and require many approvals. The closest existing placeholder was ActiveView, so I extended its functionality. I implemented a boolean variable in the task properties when you add an ActiveView to a TechDraw page. This boolean property, if set to True, would make the ActiveView treated as a 3D embed in the exported PDF. If set to False, it works normally like how an ActiveView would be exported in a regular PDF.

### 4. TechDraw Implementation Phase 2

During implementation, I discovered a problem. The ActiveView object in TechDraw never stored information about which objects it had captured in the view. This meant that when exporting to PDF, we never knew which objects had to be exported as PRC format. To solve this issue, I had to create a new variable called "Source" - a property within ActiveView that would store an array of objects that are in the ActiveView object. This way, the system knows exactly which 3D objects need to be included in the interactive PDF.

### 5. 3D PDF Export Directly from TechDraw Workbench

The final accomplishment was creating a complete PDF export system within the TechDraw workbench. I created a PDF export button in the panel of TechDraw workbench. When pressed, if no ActiveViews are found in the page that have the boolean property set to True, it will work as a normal PDF export. But if the page contains at least one 3D ActiveView, it would first export the drawing as a 2D PDF and then replace the ActiveView image with a 3D embed. This creates professional technical documents that combine traditional engineering drawings with interactive 3D models.

## User Flow

The following steps explain how to use the 3D PDF Export feature in FreeCAD after building the project from my branch.

**Step 1: Clone and Build**

First, you need to clone my FreeCAD repository and switch to the correct branch that contains the 3D PDF export feature. Open your terminal or command prompt and run these commands:

```bash
git clone https://github.com/chiragsingh1711/FreeCAD.git
```

```bash
cd FreeCAD
```

```bash
git checkout 3d-pdf-export-pr-2
```

After switching to the `3d-pdf-export-pr-2` branch, build the FreeCAD project following your usual build process for your operating system (Linux, Windows, or macOS).

**Step 2: Open or Create a Document**

Once FreeCAD is built and running, open an existing document or create a new one. If creating a new document, go to the Part Workbench and add a primitive shape (like a cube, sphere, or cylinder) to the document, which you will later export.

<img width="3839" height="2399" alt="image" src="https://github.com/user-attachments/assets/1e9c8a4a-145f-4287-8f92-7ed64f03c0d5" />


**Step 3: Switch to TechDraw Workbench**

Next, switch to the TechDraw workbench. This workbench is where you will create the technical drawing page and add the interactive 3D views.

<img width="2051" height="1163" alt="image" src="https://github.com/user-attachments/assets/6ebb7c6b-4e6e-488c-9432-b3a366caf6d9" />


**Step 4: Add a New Page**

In TechDraw, add a new empty page or select a pre-made template page to start your technical drawing.

<img width="3839" height="2399" alt="image" src="https://github.com/user-attachments/assets/27daaa49-1d51-4fa7-9992-22887e5695f9" />


**Step 5: Add an ActiveView and Enable 3D Export**

Add an ActiveView to your TechDraw page. In the ActiveView’s properties, set the "3D PDF Export" boolean property to True. This tells FreeCAD to treat this view as a 3D embed in the exported PDF.

<img width="1559" height="1677" alt="image" src="https://github.com/user-attachments/assets/25fcdf8f-7360-44df-9fef-8645ba4756f2" />


**Step 6: Export the PDF**

Click the PDF Export button in the TechDraw workbench. This will export the current drawing and include the 3D embed for any ActiveViews marked for 3D export.

<img width="2999" height="1756" alt="image" src="https://github.com/user-attachments/assets/09444eba-69a4-440d-8613-fe10f3f7b48e" />


**Step 7: Save the PDF**

Choose the location on your computer where you want to save the exported PDF file.

<img width="3809" height="1674" alt="image" src="https://github.com/user-attachments/assets/2f2892c2-9bfb-45e0-aad9-d869e4607078" />


## Result

Open the saved PDF in a compatible viewer such as Adobe Reader. You will see the technical drawing along with the interactive 3D model embedded in the PDF. You can rotate, pan, and zoom the 3D model directly within the PDF document.

<img width="3823" height="2399" alt="image" src="https://github.com/user-attachments/assets/b9159db1-8e77-470d-bb3e-a6fe232e4ae5" />


## Future Work

While the project has achieved its main goals, there are several areas for potential improvement and expansion:

**Performance Optimization:** Improve the efficiency of PRC export for large and complex FreeCAD assemblies. Currently, the export process can be slow when dealing with models that have hundreds of parts or very detailed geometry. Future work could focus on implementing better mesh optimization algorithms and parallel processing to speed up the export process.

**Let the user add 3D annotations:** Implement functionality to allow users to add interactive annotations, dimensions, and callouts directly to the 3D embeds in PDF. This would make the exported PDFs even more useful for technical documentation by allowing users to highlight specific features and add explanatory text that appears when viewing the 3D model.

**Code Quality and Improvements:** Refactor and optimize the existing codebase to follow FreeCAD's coding standards more closely. This includes better error handling, improved memory management, and more comprehensive unit testing. Also, implement direct NURBS-to-PRC conversion for maximum fidelity instead of the current mesh-based approach.

**Documentation and Tutorials:** Create comprehensive documentation and tutorial videos for users to help them understand how to use the 3D PDF export feature effectively. This includes step-by-step guides, best practices for different use cases, and troubleshooting common issues that users might encounter.

**Future Implementation Files:**

- [Download sample 3D PDF - 1](images/pdfs/Future-Improvements-1.pdf?raw=true)
- [Download sample 3D PDF - 2](images/pdfs/Future-Improvements-2.pdf?raw=true)

## Code Integration

The code for this project is still in PR review, but here are all my commits and the links to Draft and Final PR:

**Link to my commits:** [All commits related to 3D PDF Export implementation](https://github.com/FreeCAD/FreeCAD/pull/23335/commits)

**Link to draft PR:** [Draft Pull Request with initial implementation](https://github.com/FreeCAD/FreeCAD/pull/22430)

**Link to Final PR:** [Final Pull Request with complete feature implementation](https://github.com/FreeCAD/FreeCAD/pull/23335)

The implementation includes new C++ classes for PRC export, Python API extensions for user interface integration, and modifications to the TechDraw workbench to support 3D views. All code follows FreeCAD's development guidelines and has been tested on multiple platforms including Linux, Windows, and macOS.

## Conclusion

Participating in Google Summer of Code 2025 has been an incredible learning experience. I'm grateful for the opportunity to contribute to FreeCAD and the broader open-source community. Working on this 3D PDF export feature has not only improved my skills in C++ and Python development but also deepened my understanding of CAD software architecture and 3D file formats. The project addresses a real need in the FreeCAD community and will help users create better technical documentation. I look forward to seeing how this work will be used and expanded upon in the future.

## Acknowledgments

I would like to thank my mentors Pieter, Turan and Frank for their guidance and support throughout this project. Their expertise in FreeCAD development and constructive feedback helped shape this project into something truly useful for the community. I also want to express my gratitude to the FreeCAD community members for their feedback and assistance throughout the project. The active discussions on the forum and testing by community members were invaluable in making sure the feature works well for real-world use cases.
