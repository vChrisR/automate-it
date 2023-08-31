---
title: 'vRA 7 Blueprint designer preview'
date: Thu, 22 Oct 2015 10:30:25 +0000
draft: false
tags: ['Blueprint', 'preview', 'vRA', 'vRA7', 'vRealize', 'vRealize Automation', 'vRealize Automation']
---

One of the big changes you'll see in vRA 7 is the new blueprint designer. If you can't wait until the GA release, here is a vRA 7 bleuprint designer preview. In summary these are the differences from the old pre-7 blueprints:

*   Graphical canvas
*   Unified blueprint. No difference between single machine, multi-machine or application blueprints
*   NSX integration. You can now drag and drop NSX object into your blueprint canvas
*   Ability to use blueprints in blueprints Yes, nested blueprints!
*   Ability to set dependencies between elements in the blueprint
*   Ability to use vRO workflow in a blueprint. This is now called XaaS, formerly ASD
*   Last but not least: The designer is on a separate tab in the vRA GUI not hidden in the infrastructure tab.

So basically all the good elements of application director (or appD or application services) are now integrated into the vRA blueprint designer. On top of that we can now use workflows directly in a blueprint. Which is exciting to me really. This is what the designer looks like when you just created a new blueprint: [![vra7-canvas-empty](http://www.automate-it.today/wp-content/uploads/2015/10/vra7-canvas-empty-300x170.png)](http://www.automate-it.today/wp-content/uploads/2015/10/vra7-canvas-empty.png) And this is what it looks like when you would create a typical two tier application: [![vra7-canvas-populated](http://www.automate-it.today/wp-content/uploads/2015/10/vra7-canvas-populated-300x153.png)](http://www.automate-it.today/wp-content/uploads/2015/10/vra7-canvas-populated.png) Please note that these screenshots are taken from a BETA version so the GA release might look a little bit different. As you can tell from the screenshots all features are now nicely integrated. You no longer need to hack NS integration into the product or use vRO to actually integrate app services and vRA. I don't want to complain because this is a huge step forward but.... The thing I'm still missing though is a higher abstraction layer on top of the blueprints. The bueprint author still has to decide one which reservation policy the VM will land for example. If you have different environments you'll end up with blueprint sprawl. A better approach would be a form that asks functional questions like: is it for dev or prod? and then populates the different properties in the blueprint. I used to build that kind of functionality in workflows and then publish those in the catalog instead of the actual blueprints. Hopefully VMware will build something into the product to facilitate this.