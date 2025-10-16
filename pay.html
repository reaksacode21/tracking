 // --- CORE DATA & SETUP ---
    const STORAGE_KEY = 'finance_tracker_data';
    const TWO_DAYS_MS = 2 * 24 * 60 * 60 * 1000;
    let appData = {
        transactions: [],
        goals: []
    };

    /** Utility function to generate unique IDs */
    const uuid = () => crypto.randomUUID();

    /** Utility to format currency */
    const formatCurrency = (amount) => new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
    }).format(amount);

    /** Custom Message Box (replaces alert/confirm) */
    const showMessage = (title, text, isConfirm = false) => {
        const container = document.getElementById('message-box-container');
        document.getElementById('message-box-title').textContent = title;
        document.getElementById('message-box-text').textContent = text;
        const btnOk = document.getElementById('message-box-ok');
        const btnConfirm = document.getElementById('message-box-confirm');

        btnOk.onclick = () => container.classList.add('hidden');
        btnConfirm.onclick = () => container.classList.add('hidden');

        if (isConfirm) {
            btnOk.textContent = 'Cancel';
            btnConfirm.classList.remove('hidden');
            return new Promise(resolve => {
                btnConfirm.onclick = () => { container.classList.add('hidden'); resolve(true); };
                btnOk.onclick = () => { container.classList.add('hidden'); resolve(false); };
                container.classList.remove('hidden');
            });
        }

        btnOk.textContent = 'OK';
        btnConfirm.classList.add('hidden');
        container.classList.remove('hidden');
    };


    // --- DATA PERSISTENCE & CLEANUP ---

    /** Loads data from localStorage, initializes if empty, and cleans up expired reversal records. */
    const loadData = () => {
        try {
            const storedData = localStorage.getItem(STORAGE_KEY);
            if (storedData) {
                const parsedData = JSON.parse(storedData);
                appData = {
                    transactions: parsedData.transactions || [],
                    goals: parsedData.goals || []
                };
            }
        } catch (e) {
            console.error("Could not load data from localStorage:", e);
        }
        cleanUpOldTransactions();
    };

    /** Saves current data to localStorage. */
    const saveData = () => {
        try {
            localStorage.setItem(STORAGE_KEY, JSON.stringify(appData));
        } catch (e) {
            console.error("Could not save data to localStorage:", e);
        }
    };

    /**
     * Physically removes transactions that were marked for delayed deletion and have expired.
     */
    const cleanUpOldTransactions = () => {
        const initialCount = appData.transactions.length;
        const now = Date.now();
        const activeTransactions = appData.transactions.filter(t =>
            !(t.pendingDelete === true && t.deleteAfter && t.deleteAfter < now)
        );
        appData.transactions = activeTransactions;
        if (appData.transactions.length < initialCount) {
             console.log(`Cleaned up ${initialCount - appData.transactions.length} expired reversal transactions.`);
             saveData();
        }
    };

    /** Returns only transactions that are NOT marked for pending deletion (i.e., currently active). */
    const getActiveTransactions = () => {
        return appData.transactions
            .filter(t => t.pendingDelete !== true)
            .sort((a, b) => new Date(b.date) - new Date(a.date));
    };

    // --- CORE PROCESSING & CALCULATIONS ---

    /** Calculates the current account balance. */
    const calculateBalance = () => {
        return getActiveTransactions().reduce((sum, t) => {
            const amount = parseFloat(t.amount);
            return t.type === 'income' ? sum + amount : sum - amount;
        }, 0);
    };

    /** Calculates totals for a given array of transactions by type and tag. */
    const calculateTotals = (transactions) => {
        let income = 0;
        let expense = 0;
        const expensesByTag = {};

        transactions.forEach(t => {
            const amount = parseFloat(t.amount);
            if (t.type === 'income') {
                income += amount;
            } else {
                expense += amount;
                const tag = t.tag.toLowerCase();
                expensesByTag[tag] = (expensesByTag[tag] || 0) + amount;
            }
        });
        return { income, expense, expensesByTag };
    };

    /** Filters transactions based on a time period (daily, weekly, monthly, yearly). */
    const filterTransactionsByPeriod = (period) => {
        const now = new Date();
        const filtered = getActiveTransactions().filter(t => {
            const tDate = new Date(t.date);
            if (period === 'daily') {
                return tDate.toDateString() === now.toDateString();
            } else if (period === 'weekly') {
                const oneWeekAgo = new Date(now);
                oneWeekAgo.setDate(now.getDate() - 7);
                return tDate >= oneWeekAgo;
            } else if (period === 'monthly') {
                return tDate.getMonth() === now.getMonth() && tDate.getFullYear() === now.getFullYear();
            } else if (period === 'yearly') {
                return tDate.getFullYear() === now.getFullYear();
            }
            return true;
        });
        return filtered;
    };

    // --- TRANSACTION MANAGEMENT (INCLUDING REVERSAL) ---

    /**
     * "Deletes" a transaction by creating an opposite reversal transaction
     * and marking both for 48-hour delayed physical deletion.
     */
    const reverseTransaction = async (id) => {
        const confirmed = await showMessage('Confirm Reversal', 'This will create an opposite transaction and hide both from reports. They will be permanently removed in 48 hours. Proceed?', true);

        if (!confirmed) return;

        const originalTx = appData.transactions.find(t => t.id === id);
        if (!originalTx) {
            showMessage('Error', 'Original transaction not found.');
            return;
        }

        // 1. Create the Reversal Transaction
        const reversalAmount = parseFloat(originalTx.amount);
        const reversalType = originalTx.type === 'income' ? 'expense' : 'income';
        const reversalTx = {
            id: uuid(),
            type: reversalType,
            amount: reversalAmount.toFixed(2),
            tag: `REVERSAL of ${originalTx.tag}`,
            description: `Auto-reversal for ID: ${originalTx.id}`,
            date: new Date().toISOString(),
            isReversal: true,
            reversalId: originalTx.id,
            pendingDelete: true,
            deleteAfter: Date.now() + TWO_DAYS_MS // Mark for 48h deletion
        };

        // 2. Mark Original Transaction for Deletion
        originalTx.pendingDelete = true;
        originalTx.deleteAfter = Date.now() + TWO_DAYS_MS; // Mark for 48h deletion

        // 3. Add the reversal and update the original
        appData.transactions.push(reversalTx);

        // 4. Save and Update UI
        saveData();
        renderDashboard();
        renderReport('daily'); // Re-render reports to reflect immediate hiding
        showMessage('Success', 'Transaction reversed successfully. The transaction and its reversal are now hidden and will be physically purged after 48 hours.');
    };

    // --- RENDERING FUNCTIONS ---

    const renderDashboard = () => {
        const balance = calculateBalance();
        const monthlyTransactions = filterTransactionsByPeriod('monthly');
        const totals = calculateTotals(monthlyTransactions);

        // 1. Balance Overview
        document.getElementById('current-balance').textContent = formatCurrency(balance);
        document.getElementById('balance-overview').classList.toggle('bg-danger', balance < 0);
        document.getElementById('balance-overview').classList.toggle('bg-primary', balance >= 0);

        // 2. Income vs Expense Bar
        const totalFlow = totals.income + totals.expense;
        const incomeWidth = totalFlow > 0 ? (totals.income / totalFlow) * 100 : 0;
        const expenseWidth = totalFlow > 0 ? (totals.expense / totalFlow) * 100 : 0;

        const barHtml = `
            <div style="width: ${incomeWidth}%;" class="bg-secondary h-full transition-all duration-500"></div>
            <div style="width: ${expenseWidth}%;" class="bg-danger h-full transition-all duration-500"></div>
        `;
        document.getElementById('income-expense-bar').innerHTML = barHtml;
        document.getElementById('monthly-income').textContent = `I: ${formatCurrency(totals.income)}`;
        document.getElementById('monthly-expense').textContent = `E: ${formatCurrency(totals.expense)}`;

        // 3. Category Pie List (Simplified)
        const expensesByTag = totals.expensesByTag;
        const sortedTags = Object.entries(expensesByTag)
            .sort(([, a], [, b]) => b - a)
            .slice(0, 5); // Show top 5

        const totalExpense = totals.expense;
        let pieHtml = sortedTags.map(([tag, amount]) => {
            const percentage = ((amount / totalExpense) * 100).toFixed(1);
            const colorClass = ['bg-red-400', 'bg-blue-400', 'bg-yellow-400', 'bg-green-400', 'bg-purple-400'][sortedTags.findIndex(([t]) => t === tag)];
            return `
                <div class="flex justify-between items-center">
                    <span class="capitalize flex items-center">
                        <span class="w-2 h-2 rounded-full mr-2 ${colorClass}"></span>
                        ${tag}
                    </span>
                    <span class="font-medium text-gray-800">${percentage}% (${formatCurrency(amount)})</span>
                </div>
            `;
        }).join('');
        document.getElementById('category-pie-list').innerHTML = pieHtml || '<p class="text-gray-500 text-center">No expenses recorded this month.</p>';

        // 4. Savings Goal Progress
        renderGoalsProgress(monthlyTransactions);

        // 5. Analytics & Insights
        renderInsights(monthlyTransactions, totals);
    };

    const renderGoalsProgress = (monthlyTransactions) => {
        const container = document.getElementById('goals-progress-container');
        
        // Populate the goal selection dropdown
        populateGoalSelect();

        if (appData.goals.length === 0) {
            container.innerHTML = '<h3 class="font-semibold text-lg mb-3 text-gray-800">Savings Goals</h3><p class="text-gray-500">No goals set yet. Set one in the Add/Goal tab!</p>';
            return;
        }

        let goalsHtml = appData.goals.map(goal => {
            // CRITICAL LOGIC: Calculate savings by summing ALL transaction amounts tagged with the goal name.
            // Since contributions are recorded as EXPENSE transactions (reducing the main balance), 
            // we treat their absolute value as a positive contribution to the goal fund itself.
            const savedSoFar = appData.transactions
                .filter(t => t.tag === goal.name)
                .reduce((sum, t) => sum + parseFloat(t.amount), 0);

            const progressPercent = Math.min(100, (savedSoFar / goal.targetAmount) * 100).toFixed(1);

            return `
                <div class="mt-4 p-3 bg-gray-50 rounded-lg">
                    <h4 class="font-medium text-lg">${goal.name} (${formatCurrency(goal.targetAmount)})</h4>
                    <p class="text-xs text-gray-500">Saved: ${formatCurrency(savedSoFar)}</p>

                    <div class="w-full bg-gray-200 rounded-full h-2.5 mt-2">
                        <div class="bg-primary h-2.5 rounded-full transition-all duration-500" style="width: ${progressPercent}%"></div>
                    </div>
                    <p class="text-right text-sm font-medium text-primary mt-1">${progressPercent}% Complete</p>
                </div>
            `;
        }).join('');

        container.innerHTML = `<h3 class="font-semibold text-lg mb-3 text-gray-800">Savings Goals</h3>${goalsHtml}`;
    };

    /** Populates the goal selection dropdown for contributions. */
    const populateGoalSelect = () => {
        const select = document.getElementById('contribution-goal');
        const currentSelectedValue = select.value;
        select.innerHTML = '<option value="" disabled selected>Select a goal...</option>';

        if (appData.goals.length === 0) {
            select.innerHTML = '<option value="" disabled selected>No goals set</option>';
            return;
        }

        appData.goals.forEach(goal => {
            const option = document.createElement('option');
            option.value = goal.name;
            option.textContent = goal.name;
            if (goal.name === currentSelectedValue) {
                option.selected = true;
            }
            select.appendChild(option);
        });
    };


    const renderReport = (period) => {
        const filteredTransactions = filterTransactionsByPeriod(period);
        const totals = calculateTotals(filteredTransactions);
        const container = document.getElementById('report-details');
        document.getElementById('report-title').textContent = `${period.charAt(0).toUpperCase() + period.slice(1)} Report`;

        // Update active filter button style
        document.querySelectorAll('.report-filter-btn').forEach(btn => {
            btn.classList.remove('bg-primary', 'text-white');
            btn.classList.add('bg-gray-200', 'text-gray-700');
        });
        document.querySelector(`.report-filter-btn[data-period="${period}"]`).classList.remove('bg-gray-200', 'text-gray-700');
        document.querySelector(`.report-filter-btn[data-period="${period}"]`).classList.add('bg-primary', 'text-white');


        // Summary (Weekly/Monthly/Yearly)
        let summaryHtml = '';
        if (period !== 'daily') {
            const sortedTags = Object.entries(totals.expensesByTag).sort(([, a], [, b]) => b - a);
            summaryHtml = `
                <div class="p-3 bg-blue-50 rounded-lg mb-4">
                    <p class="font-semibold text-primary">Income: ${formatCurrency(totals.income)}</p>
                    <p class="font-semibold text-danger">Expense: ${formatCurrency(totals.expense)}</p>
                    <p class="font-bold mt-2">Net Flow: ${formatCurrency(totals.income - totals.expense)}</p>
                </div>
                <h4 class="font-medium mb-2">Category Summary:</h4>
                <ul class="list-disc list-inside text-sm space-y-1">
                    ${sortedTags.map(([tag, amount]) => `
                        <li><span class="capitalize">${tag}</span>: ${formatCurrency(amount)}</li>
                    `).join('') || '<li>No expenses recorded.</li>'}
                </ul>
                <hr class="my-4">
            `;
        }

        // Transaction List
        let txListHtml = '';
        if (filteredTransactions.length === 0) {
            txListHtml = '<p class="text-center text-gray-500 mt-4">No transactions found for this period.</p>';
        } else {
            txListHtml = filteredTransactions.map(t => {
                const isExpense = t.type === 'expense';
                const color = isExpense ? 'text-danger' : 'text-secondary';
                const sign = isExpense ? '-' : '+';
                const dateString = new Date(t.date).toLocaleDateString();

                return `
                    <div class="flex justify-between items-center p-3 bg-white rounded-lg data-card border-l-4 ${isExpense ? 'border-danger/50' : 'border-secondary/50'}">
                        <div>
                            <p class="font-semibold text-gray-800 capitalize">${t.tag}</p>
                            <p class="text-xs text-gray-500">${t.description || 'No description'}</p>
                            <p class="text-xs text-gray-400">${dateString}</p>
                        </div>
                        <div class="flex items-center space-x-2">
                            <span class="font-bold ${color} text-lg">${sign}${formatCurrency(t.amount)}</span>
                            <button onclick="reverseTransaction('${t.id}')" class="text-gray-400 hover:text-danger p-1 rounded-full transition-colors duration-150" title="Reverse Transaction">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                    <path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd" />
                                </svg>
                            </button>
                        </div>
                    </div>
                `;
            }).join('');
        }

        document.getElementById('report-summary').innerHTML = summaryHtml;
        document.getElementById('report-transactions').innerHTML = txListHtml;
    };

    // --- ANALYTICS & INSIGHTS ---

    const renderInsights = (monthlyTransactions, currentTotals) => {
        let insights = [];

        // 1. Spending Trends (Compare current month to previous month)
        const now = new Date();
        const lastMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1);
        const lastMonthTransactions = getActiveTransactions().filter(t => {
            const tDate = new Date(t.date);
            return tDate.getMonth() === lastMonth.getMonth() && tDate.getFullYear() === lastMonth.getFullYear();
        });
        const lastTotals = calculateTotals(lastMonthTransactions);

        const diff = currentTotals.expense - lastTotals.expense;
        const trend = diff / (lastTotals.expense || 1) * 100;

        if (trend > 10) {
            insights.push(`<p><span class="text-danger font-medium">Spending Surge!</span> Your total expenses rose by <span class="font-bold">${trend.toFixed(1)}%</span> compared to last month. Watch those categories!</p>`);
        } else if (trend < -10) {
            insights.push(`<p><span class="text-secondary font-medium">Great Work!</span> Your spending decreased by <span class="font-bold">${Math.abs(trend).toFixed(1)}%</span> this month. Keep up the saving!</p>`);
        } else {
             insights.push('<p>Your overall spending trend is stable. Consistent and steady financial flow.</p>');
        }

        // 2. Highlight Top Expense Categories
        const topTagEntry = Object.entries(currentTotals.expensesByTag).sort(([, a], [, b]) => b - a)[0];
        if (topTagEntry) {
            const [topTag, topAmount] = topTagEntry;
            insights.push(`<p>Your top expense category is <span class="font-bold capitalize">${topTag}</span>, accounting for ${formatCurrency(topAmount)} this month.</p>`);
        }

        // 3. Forecast Next Month's Savings
        const avgMonthlyExpense = (currentTotals.expense + lastTotals.expense) / 2;
        const avgMonthlyIncome = (currentTotals.income + lastTotals.income) / 2;
        const forecastSavings = avgMonthlyIncome - avgMonthlyExpense;

        if (forecastSavings > 0) {
            insights.push(`<p>Based on your last two months, you are forecasted to save around <span class="font-bold text-secondary">${formatCurrency(forecastSavings)}</span> next month.</p>`);
        } else {
            insights.push(`<p>A cautious forecast suggests a potential deficit of ${formatCurrency(Math.abs(forecastSavings))}. Time to review spending!</p>`);
        }

        document.getElementById('insights-list').innerHTML = insights.join('<hr class="my-2 border-gray-100">');
    };


    // --- EVENT LISTENERS ---

    const setupEventListeners = () => {
        // Tab switching
        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
                e.target.classList.add('active');

                document.querySelectorAll('.content-view').forEach(view => view.classList.add('hidden'));
                const viewId = e.target.getAttribute('data-view');
                document.getElementById(viewId).classList.remove('hidden');

                // Re-render dashboard when switching back
                if (viewId === 'dashboard') {
                    renderDashboard();
                } else if (viewId === 'reports') {
                    // Default to daily report when reports tab is opened
                    renderReport('daily');
                }
            });
        });

        // Report filtering
        document.querySelectorAll('.report-filter-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const period = e.target.getAttribute('data-period');
                renderReport(period);
            });
        });

        // Transaction Form Submission
        document.getElementById('transaction-form').addEventListener('submit', (e) => {
            e.preventDefault();
            const form = e.target;
            const amount = parseFloat(form.elements.amount.value).toFixed(2);
            const type = form.elements.type.value;
            const tag = form.elements.tag.value.trim();
            const description = form.elements.description.value.trim();

            const newTransaction = {
                id: uuid(),
                type,
                amount,
                tag,
                description,
                date: new Date().toISOString(),
                isReversal: false,
                reversalId: null,
                pendingDelete: false,
                deleteAfter: null
            };

            appData.transactions.push(newTransaction);
            saveData();
            renderDashboard();
            form.reset();
            showMessage('Success', `${type.charAt(0).toUpperCase() + type.slice(1)} of ${formatCurrency(amount)} (${tag}) added!`);
        });

        // Goal Form Submission
        document.getElementById('goal-form').addEventListener('submit', (e) => {
            e.preventDefault();
            const form = e.target;
            const name = form.elements['goal-name'].value.trim();
            const targetAmount = parseFloat(form.elements['goal-target'].value);
            const monthlyTarget = parseFloat(form.elements['goal-monthly'].value);

            const newGoal = {
                id: uuid(),
                name,
                targetAmount,
                monthlyTarget,
                dateSet: new Date().toISOString()
            };

            appData.goals.push(newGoal);
            saveData();
            renderDashboard();
            populateGoalSelect(); // Update the contribution dropdown
            form.reset();
            showMessage('Goal Set', `Savings goal "${name}" for ${formatCurrency(targetAmount)} set successfully!`);
        });

        // Contribution Form Submission
        document.getElementById('contribution-form').addEventListener('submit', (e) => {
            e.preventDefault();
            const form = e.target;
            const amount = parseFloat(form.elements['contribution-amount'].value).toFixed(2);
            const goalName = form.elements['contribution-goal'].value;

            // Record as an EXPENSE (transfer out) tagged with the goal name.
            // This reduces the main balance correctly and allows renderGoalsProgress to track it.
            const newContribution = {
                id: uuid(),
                type: 'expense', // It's an expense from the main account
                amount,
                tag: goalName, // Tag is the Goal Name for tracking
                description: `Contribution to ${goalName}`,
                date: new Date().toISOString(),
                isReversal: false,
                reversalId: null,
                pendingDelete: false,
                deleteAfter: null
            };

            appData.transactions.push(newContribution);
            saveData();
            renderDashboard();
            form.reset();
            // Re-select the goal for convenience, or reset completely. Resetting for simplicity.
            document.getElementById('contribution-goal').value = ""; 
            showMessage('Contribution Added', `${formatCurrency(amount)} contributed to "${goalName}"!`);
        });
    };

    // --- INITIALIZATION ---
    window.onload = () => {
        loadData();
        setupEventListeners();
        renderDashboard();
        // Initial population of the goal select box
        populateGoalSelect(); 
    };
