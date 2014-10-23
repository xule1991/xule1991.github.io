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

This means for if, else, while clause, it is better to let the block in them has just one line, the line of calling a function which name means the operation it will take,and that will make the code easier for reading.


	乐此不疲～

2014-10-23 15:38:11









































































