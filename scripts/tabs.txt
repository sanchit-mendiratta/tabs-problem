/*!
 * --------------------------------------- START OF LICENSE NOTICE ------------------------------------------------------
 * Copyright (c) 2018 Software Robotics Corporation Limited ("Soroco"). All rights reserved.
 *
 * NO WARRANTY. THE PRODUCT IS PROVIDED BY SOROCO "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT
 * SHALL SOROCO BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
 * BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
 * INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
 * NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THE PRODUCT, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH
 * DAMAGE.
 * ---------------------------------------- END OF LICENSE NOTICE -------------------------------------------------------
 *
 *   Candidate: Sanchit Mendiratta - <sanchit.zidane@gmail.com>
 *   Purpose: Soroco Front-end hands on assignment
 */

/*
    Function to render existing tab, can be called anywhere in the application with a user object of type { "name": "", "login": }
 */
function renderTab(user) {
	if (states.tabsList.length > 3) {
		// add conditions to support dynamic width of tabs
		// combined width of tabs are more than allowed
		renderSeeMore();
	} else {
		let temp =
		`<div id=${states.tabsList.length} class="src-tab" onclick="setActiveOnClick()">
      		<div class="tab-line1">` +
				user.name +
			`</div>
      		<div class="tab-line2">` +
				user.login +
			`</div>
      		<i class="material-icons" onclick="closeTab()">close</i>
    	</div>`;
		$("#dynamic-tabs").append(temp);
		setActiveTab(states.tabsList.length);
	}
}

/*
   Function to create a new tab and takes values from the input fields on UI
*/
function createTab() {
	let uname = $("#user-name").val();
	let login = $("#user-login").val();
	if (uname && uname.length > 0 && login && login.length > 0) {
		let user = {
			id: states.tabsList.length + 1,
			name: uname,
			login: login
		};

		states.tabsList.push(user); // append newly created tab to "tabsList"
		renderTab(user);
	}
	// reset "Add transaction" fields
	$("#user-name").val("");
	$("#user-login").val("");
}

/*
   Function to close a tab
*/
function closeTab() {
	let el = event.target.parentElement;
	let id = el.id;
	el.parentElement.removeChild(el);
	let el2 = $("#"+id+"details");
	el2.remove();
	states.tabsList = states.tabsList.filter(user => user.id != id);
	event.stopImmediatePropagation();
	rerender();
}

/*
   Function to set a particular tab-heading as active when clicked on it
*/
function setActiveTab(id) {
	if(isNaN(parseInt(id, 10))) return;
	if (states.tabsList.length > 0) {
		$(".add-transaction-tab").removeClass("active");
	}
	let el = $("#" + id);
	el.addClass("active");
	states.tabsList
		.filter(user => user.id != id)
		.forEach(user => $("#" + user.id).removeClass("active"));
	getTabDetails(id);
}
function setActiveOnClick() {
	let id = event.target.parentElement.id;
	setActiveTab(id);
}

/*
   Function to fetch/render tab details of particular tab while switching between tabs
*/
function getTabDetails(id) {
	if(isNaN(parseInt(id, 10))) return;
	let user = states.tabsList.filter(user => user.id == id);
	let domel = $('#'+id+'details');
	if(domel.length == 0) {
		let temp =
		`<div id=${id+"details"}>
			<p>Hi ${user[0].name}</p>
		</div>`;
		$("#active-tab-details").append(temp);
	}
	hideAllOthers(id);
	$("#"+id+"details").removeClass("hidden");
	$("#active-tab-details").removeClass("hidden");
	$('#add-transaction').addClass("hidden");
}

function hideAllOthers(id) {
	states.tabsList
		.filter(user => user.id != id)
		.forEach(user => $("#"+user.id+"details").addClass("hidden"));
}

/*
   Function to render See more tabs when the length of tabs is more than 3. Please try to make this based on the screen width available
   and compute the number of tabs to be visible on the screen accordingly.
*/
function renderSeeMore() {
	if ($("#more-list").length) {
		$("#more-count").text(states.tabsList.length - 3 + " More");
	} else {
		var temp =
			`<div id="seemore" class = "src-tab" onclick="toggleDropdown()">
				<div id="more-count">
					${(states.tabsList.length - 3)} More 
					<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M7.41 8.59L12 13.17l4.59-4.58L18 10l-6 6-6-6 1.41-1.41z"/><path fill="none" d="M0 0h24v24H0V0z"/></svg>
				</div>
				<div id="more-list" class="hidden"></div>
			</div>`;

		$("#dynamic-tabs").append(temp);
	}
	populateDropdown();
}

/*
   Function to populate the see more menu dropdown list
*/
function populateDropdown() {
	$("#more-list").html("");
	for (var i = 3; i < states.tabsList.length; i++) {
		let temp =
			`<div id=${i+1} class="more-list-item" onclick="switchDropdown()">
				<div class="list-item1">` +
					states.tabsList[i].name +
				`</div>
				<div class="list-item2">` +
					states.tabsList[i].login +
				`</div>
			</div>`;
		$("#more-list").append(temp);
	}
}

/*
   Function to show/hide the dropdown list when Seem more tab is clicked
*/
function toggleDropdown() {
	$("#more-list").removeClass("hidden");
	$("#more-list").toggle("hidden");
}

/*
   Function to switch to a tab selected from the see more list and set it as the active tab visible on the screen,
  while one of the tabs earlier visible on the screen gets added to the dropdown list
*/
function switchDropdown() {
	let id = parseInt(event.target.parentElement.id);
	if(isNaN(id)) return;
	states.tabsList = states.tabsList.map(user => {
		if(user.id == 3) {
			user.id = id;
			return user;
		}
		if(user.id == id) {
			user.id = 3;
			return user;
		}
		return user;
	});
	let user = states.tabsList.filter(user => user.id == 3);
	let userId = states.tabsList.filter(user => user.id == id);
	$("#3 .tab-line1").text(user[0].name);
	$("#3 .tab-line2").text(user[0].login);
	$("#3details p").text("Hi "+user[0].name);
	$("#"+id+" .list-item1").text(userId[0].name);
	$("#"+id+" .list-item2").text(userId[0].login);
	setActiveTab(3);
}

function setAddTransactionActive() {
	$("#active-tab-details").addClass("hidden");
	$('#add-transaction').removeClass("hidden");
}

function rerender() {
	$("#dynamic-tabs").children().remove();
	$("#active-tab-details").children().remove();
	let tabs = states.tabsList;
	states.tabsList = [];
	tabs.forEach(user => {
		states.tabsList.push({
			id: states.tabsList.length + 1,
			name: user.name,
			login: user.login
		});
		renderTab({
			name: user.name,
			login: user.login
		});
	});
}
