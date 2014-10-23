---
layout: post
title: CleaningCode1
tags: [Reading]
---

>Make camp cleaner then it looks like when we came

Something about name

Code on Mine Clearance
sometimes we need to make code easier for reading, that will help a lot of maintainess.

www.fitnese.org

original code:
```
	public static String testableHtml (PageData pageData, boolean includeSuiteSetup) throws Exception {
		WikiPage wikiPage = pageData.getWikiPage();
		StringBuffer buffer = new StringBuffer();
		if (pageData.hasAttribute("Test")) {
			if (includeSuiteSetup) {
				WikiPage suiteSetup = 
					PageCrawlerImpl.getInheritedPage(
						SuiteResponder.SUITE_SETUP_NAME, wikiPage);
				if (suiteSetup != null) {
					WikiPagePath pagePath =
						suiteSetup.getPageCrawler().getFullPath(suiteSetup);
					
					String pagePathName = PathParser.render(pagePath);
					buffer.append("!include -setup .")
						.append(pagePathName)
						.append("\n");
				}
			}
			WikiPage setup = 
				PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
			if(setup != null) {
				WikiPagePath setupPath =
					wikiPage.getPageCrawler().getFullPath(setup);
				String setupPathName = PathParser.render(setupPath);
				buffer.append("!include -setup .")
					.append(setupPathName)
					.append("\n");
			}
			buffer.append(pageData.getContent());
			if (pageData.hasAttribute("Test")) {
				WikiPage teardown =
					PageCrawlerImple.getInheritedPage("TearDown", wikiPage);
				if(teardown !=null) {
					WikiPagePath tearDownPath = 
						wikiPage.getPageCrawler().getFullPath(teardown);
					String tearDownPathName = PathParser.render(tearDownPath);
					buffer.append("\n")
						.append("!include -teardown .")
						.append(tearDownPathName)
						.append("\n");
				}
				if(includeSuiteSetup) {
					WikiPage suiteTeardown =
						PageCrawlerImpl.getInheritedPage(
						SuiteResponder.SUITE_TEARDOWN_NAME,
						wikiPage
					);
					if(suiteTeardown != null) {
						WikiPagePath pagePath =
							suiteTeardown.getPageCrawler().getFullpath(suiteTeardown);
						String pagePathName = PathParser.render(pagePath);
						buffer.append("!include -teardown .")
							.append(pagePathName)
							.append("\n");
					}
				}
			}
			pageData.setContent(buffer.toString());
			return pageData.getHtml();
		}
	}
	
change to:
	
	public static String renderPageWithSetupAndTeardowns(
		PageData pageData, boolean isSuite) throws Exception {
		boolean isTestPage = pageData.hasAttribute("Test");
		if(isTestPage) {
			WikiPage testPage = pageData.getWikiPage();
			StringBuffer newPageContent = new StringBuffer();
			includeSetuppages(testPage, newPageContent, isSuite);
			newPageContent.append(pageData.getContent());
			includeTeardownPages(testPage, newPageContent, isSuite);
			pageData.setContent(newPageContent.toString());
		}
		return pageData.getHtml();
	}
```

SetupTeardownIncluder.java

```
package fitnesse.html

import fitnesse.responders.run.SuiteResponder;
import fitnesse.wiki.*;

public class SetupTeardownIncluder {
	private PageData pageData;
	private boolean isSuite;
	private WikiPage testPage;
	private StringBuffer newPageContent;
	private PageCrawler pageCrawler;
	
	public static String render(PageData pageData) throws Exception {
		return render(pageData, false);
	}
	
	public static String render(PageData pageData, boolean isSuite) throws Exception {
		return new SetupTeardownIncluder(pageData).render(isSuite);
	}
	private SetupTeardownIncluder(PageData pageData) {
		this.pageData = pageData;
		testPage = pageData.getWikiPage();
		pageCrawler = testpage.getPageCrawler();
		newpageContent = new StringBuffer();
	}
	private String render(boolean isSuite) throws Exception {
		this.isSuite = isSuite;
		if(isTestPage())
			includeSetupAndTeardownPages();
		return pageData.getHtml();
	}
	private boolean isTestPage() throws Exception{
		includeSetupPages();
		includePageContent();
		includeTeardownPages();
		updatePageContent();
	}
	private void includeSetupPages() throws Exception {
		if(isSuite)
			includeSuiteSetupPage();
		includeSetupPage();
	}
	private void includeSuiteSetupPage() throws Exception {
		include(SuiteResponder.SUITE_SETUP_NAME, "-setup");
	}
	private void includeSetupPage() throws Exception {
		include("SetUp", "-setup");
	}
	private void includePageContent() throws Exception {
		newPageContent.append(pageData.getContent());
	}
	private void includeTeardownPages() throws Exception {
		includeTeardownPage();
		if(isSuite)
			includeSuiteTeardownPage();
	}
	private void includeTeardownPages() throws Exception {
		includeTeardownPage();
		if(isSuite)
			includeSuiteTeardownPage();
	}
	private void includeTeardownPage() throws Exception {
		include("TearDown", "-teardown");
	}
	private void includeSuiteTeardownPage() throws Exception {
		include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown");
	}
	private void updatePageContent() throws Exception {
		pageData.setContent(newPageContent.toString());
	}
	private void include(String pageName, String arg) throws Exception {
		WikiPage inheritedPage = findInheritedPage(pageName);
		if(inheritedPage != null) {
			String pagePathName = getPathNameForPage(inheritedPage);
			buildIncludeDirective(pagePathName, arg);
		}
	}
	private WikiPage findInheritedPage(String pageName) throws Exception {
		return PageCrawlerImple.getInheritedPage(pageName, testPage);
	} 
	private String getPathNameForPage(WikiPage page) throws Exception {
		WikiPagePath pagePath = pageCrawler.getFullPath(page);
		return PathParser.render(pagePath);
	}
	private void buildIncludeDirective(String pagePathName, String arg) {
		newPageContent
		.append("\n!include")
		.append(arg)
		.append(" .")
		.append(pagePathName)
		.append("\n");
	}
}
```

This means for if, else, while clause, it is better to let the block in them has just one line, the line of calling a function which name means the operation it will take,and that will make the code easier for reading.

Just do one thing in a method:
	In the example, the original code do all the following things in one method:
1. build buffer, 2. get page, 3. search for the page inherited, 4. render path, 5. add mysterious string, 
6. produce html,etc.
	So,we need to decompose it.
	
To make the code in a method in the same abstraction layer,and that will make extract easier.	

Single Responsibility Principle,SRP
Open Closed Principle, OCP


	乐此不疲～

2014-10-23 15:38:11









































































