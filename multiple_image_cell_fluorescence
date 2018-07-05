// Macro IMDEA nanoscience
// Created by Alvaro del Moral for ImageJ
//Result is given by the normalized Weber contrast: (Is-Ib)/Ib for comparison between different images.
//Is -> Image signal mean
//Ib -> Image background mean

run("Close All");
print("\\Clear");

showMessage("For better results make sure all your images are RAW or Tiff");
Dialog.create("Image Loader");
	Dialog.addMessage("Fluorescence Macro\n By: Alvaro del Moral");
	Dialog.addString("Name of Result file", "");
	Dialog.addChoice("Analyse One or Multiples images", newArray("One", "Multiple"));
	Dialog.addCheckbox("Turn BatchMode On\n (Recommended)", true);
Dialog.show();
savename=Dialog.getString();
numImg= Dialog.getChoice();
batchcheck=Dialog.getCheckbox();
saveformat=(".txt")
savepath=getDirectory("Save Final Result file to");
batch=true

//BatchMode check
if(batchcheck==false) 
{
	Dialog.create("Memory Warning!");
		Dialog.addMessage("Turning BatchMode off allows to see and save all individual images");
		Dialog.addMessage("This can cause memory problems on your computer");
		Dialog.addCheckbox("Do you wish to continue?", false);
	Dialog.show();
	check=Dialog.getCheckbox();
	if(check==false) 
	{
		exit();
	}
	else
	{
		batch=false;
	}
}

if(numImg=="Multiple")
{
	Dialog.create("Multiple Image Loader");
		Dialog.addNumber("Number of Images", 3);
		Dialog.addChoice("Image Extension:", newArray("Tiff", "RAW", "JPG","Gif"))
	Dialog.show();
	inum= Dialog.getNumber();
	itype= Dialog.getChoice();
	
	idir=getDirectory("Select the Directory of the Images");
	ilist = getFileList(idir);
	ilen=ilist.length;
	if(inum>ilen)
	{
		exit("Error:\n Selected more images than files in directory\n Try Again");
	}	
}


//Processes happen in the background, increasing the speed
setBatchMode(batch);

if(numImg=="One")
{
	open();
	ImgName = File.name;

	//separate channels and eliminate blue and red signals
	rename("data");
	run("Split Channels");
	selectWindow("data (blue)");
	close();
	selectWindow("data (red)");
	close();
	selectWindow("data (green)");
	run("Duplicate...", "Duplicate green");
	rename("Green mask");
	setAutoThreshold("Default");

	//Create a background Mask
	run("Convert to Mask");
	run("Divide...", "value=255");
	rename("Background mask");

	//Create a signal mask
	run("Duplicate...", "title=[Signal mask]");
	selectWindow("Signal mask");
	run("Invert");
	run("Subtract...", "value=254");//signal mask
	imageCalculator("Multiply", "Background mask","data (green)");
	imageCalculator("Multiply", "Signal mask","data (green)");

	//Obtain Mean fluorescence for both images (background and signal image)
	run("Set Measurements...", "mean redirect=None decimal=3");
	selectWindow("Background mask");
	setAutoThreshold("Default");
	setThreshold(1,255);
	run("Analyze Particles...", "summarize");
	ImgBack = getResult("Mean");
	selectWindow("Signal mask");
	setAutoThreshold("Default");
	setThreshold(1,255);
	run("Analyze Particles...", "summarize");
	ImgSig = getResult("Mean");

	if(batch==true)
	{
		run("Close All");
	}
}

if(numImg=="Multiple")
{
	for(i=0;i<inum;i++)
		{
			open(idir+ilist[i]);
			selectWindow(ilist[i]);
			ImgName = File.name;

			//separate channels and eliminate blue and red signals
			rename("data");
			run("Split Channels");
			selectWindow("data (blue)");
			close();
			selectWindow("data (red)");
			close();
			selectWindow("data (green)");
			run("Duplicate...", "Duplicate green");
			rename("Green mask");
			setAutoThreshold("Default");
	
			//Create a background Mask
			run("Convert to Mask");
			run("Divide...", "value=255");
			rename("Background mask");

			//Create a signal mask
			run("Duplicate...", "title=[Signal mask]");
			selectWindow("Signal mask");
			run("Invert");
			run("Subtract...", "value=254");//signal mask
			imageCalculator("Multiply", "Background mask","data (green)");
			imageCalculator("Multiply", "Signal mask","data (green)");

			//Obtain Mean fluorescence for both images (background and signal image)
			run("Set Measurements...", "mean redirect=None decimal=3");
			selectWindow("Background mask");
			setAutoThreshold("Default");
			setThreshold(1,255);
			run("Analyze Particles...", "summarize");
			ImgBack = getResult("Mean");
			selectWindow("Signal mask");
			setAutoThreshold("Default");
			setThreshold(1,255);
			run("Analyze Particles...", "summarize");
			ImgSig = getResult("Mean");

			//Calculate the Weber contrast
			weber = ((ImgSig-ImgBack)/(ImgBack));

			if(batch==true)
			{
				run("Close All");
			}
		}
}


setBatchMode("show");
setBatchMode(false);

//Save results file
selectWindow("Summary");
saveAs("Text", savepath+savename);
showMessage("The results have been saved in a tab separated text file!");
