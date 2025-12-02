<!-- Fundraising widget container -->
<div
  class="circus-fundraising-widget"
  data-current="35000"  <!-- <-- replace with your live total -->
>
</div>

<style>
  :root {
    --circus-cream:   #f2f0e5;
    --circus-red:     #df4e36;
    --circus-charcoal:#282423;
    --circus-navy:    #4d566e;
    --circus-green:   #67b9b3;
  }

  .circus-fundraising-widget {
    max-width: 420px;
    margin: 1.5rem auto;
    padding: 1.25rem 1.25rem 1.5rem;
    border-radius: 18px;
    background: var(--circus-cream);
    color: var(--circus-charcoal);
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    box-shadow: 0 10px 25px rgba(0,0,0,0.12);
  }

  .circus-fundraising-header {
    text-align: center;
    margin-bottom: 0.25rem;
  }

  .circus-fundraising-title {
    font-size: 1.1rem;
    font-weight: 700;
    letter-spacing: 0.03em;
    text-transform: uppercase;
    color: var(--circus-charcoal);
  }

  .circus-fundraising-subtitle {
    font-size: 0.85rem;
    margin-top: 0.15rem;
    color: rgba(40,36,35,0.85);
  }

  .circus-match-pill {
    display: inline-flex;
    align-items: center;
    gap: 0.4rem;
    margin-top: 0.4rem;
    padding: 0.25rem 0.6rem;
    border-radius: 999px;
    background: rgba(103,185,179,0.18);
    color: var(--circus-charcoal);
    font-size: 0.8rem;
    font-weight: 500;
  }

  .circus-match-pill-tag {
    padding: 0.05rem 0.4rem;
    border-radius: 999px;
    background: var(--circus-green);
    color: var(--circus-charcoal);
    font-size: 0.7rem;
    text-transform: uppercase;
    letter-spacing: 0.06em;
    font-weight: 700;
    white-space: nowrap;
  }

  .circus-arc-wrapper {
    position: relative;
    margin: 1.2rem auto 0.75rem;
    max-width: 320px;
  }

  .circus-arc {
    width: 100%;
    display: block;
  }

  .circus-arc path {
    fill: none;
    stroke-linecap: round;
  }

  .circus-arc-bg {
    stroke: rgba(40,36,35,0.15);
    stroke-width: 12;
  }

  .circus-arc-impact {
    stroke: var(--circus-red);
    stroke-width: 12;
    stroke-linecap: round;
    stroke-dasharray: 0;
    stroke-dashoffset: 0;
    transition: stroke-dashoffset 0.8s ease-out;
  }

  .circus-arc-raw {
    stroke: var(--circus-navy);
    stroke-width: 6;
    stroke-linecap: round;
    stroke-dasharray: 0;
    stroke-dashoffset: 0;
    transition: stroke-dashoffset 0.8s ease-out 0.05s;
  }

  .circus-arc-markers circle {
    stroke-width: 2;
    stroke: var(--circus-cream);
  }

  .circus-arc-marker-upcoming {
    fill: rgba(40,36,35,0.25);
  }

  .circus-arc-marker-active {
    fill: var(--circus-green);
  }

  .circus-arc-marker-reached {
    fill: var(--circus-red);
  }

  .circus-arc-centre {
    position: absolute;
    left: 50%;
    top: 52%;
    transform: translate(-50%, -50%);
    text-align: center;
    pointer-events: none;
  }

  .circus-amount-main {
    font-size: 1.35rem;
    font-weight: 800;
    color: var(--circus-charcoal);
  }

  .circus-amount-impact {
    font-size: 0.85rem;
    margin-top: 0.1rem;
    color: rgba(40,36,35,0.9);
  }

  .circus-amount-impact strong {
    font-weight: 700;
    color: var(--circus-red);
  }

  .circus-amount-goal {
    font-size: 0.8rem;
    color: rgba(40,36,35,0.75);
    margin-top: 0.1rem;
  }

  .circus-milestones {
    margin: 0.75rem 0 0;
    padding: 0.6rem 0 0;
    border-top: 1px solid rgba(40,36,35,0.12);
    list-style: none;
  }

  .circus-milestone {
    display: grid;
    grid-template-columns: auto 1fr auto;
    column-gap: 0.55rem;
    row-gap: 0.05rem;
    align-items: flex-start;
    padding: 0.35rem 0;
  }

  .circus-milestone-dot {
    width: 0.7rem;
    height: 0.7rem;
    border-radius: 999px;
    margin-top: 0.16rem;
  }

  .circus-milestone.reached .circus-milestone-dot {
    background: var(--circus-red);
  }

  .circus-milestone.active .circus-milestone-dot {
    background: var(--circus-green);
  }

  .circus-milestone.upcoming .circus-milestone-dot {
    background: rgba(40,36,35,0.25);
  }

  .circus-milestone-main {
    font-size: 0.82rem;
    font-weight: 600;
    color: var(--circus-charcoal);
  }

  .circus-milestone-text {
    grid-column: 2 / span 2;
    font-size: 0.78rem;
    line-height: 1.35;
    color: rgba(40,36,35,0.85);
  }

  .circus-milestone-amount {
    font-size: 0.8rem;
    font-weight: 600;
    color: rgba(40,36,35,0.9);
    white-space: nowrap;
  }

  @media (max-width: 480px) {
    .circus-fundraising-widget {
      padding: 1rem 0.9rem 1.1rem;
      border-radius: 16px;
    }
    .circus-arc-centre {
      top: 55%;
    }
    .circus-fundraising-title {
      font-size: 1rem;
    }
  }
</style>

<script>
(function() {
  const container = document.querySelector('.circus-fundraising-widget');
  if (!container) return;

  // ----- CONFIG -----
  const rawAmount = Number(container.dataset.current || '0'); // actual donations
  const matchCap = 15000;   // first $15k is matched
  const matchMaxImpact = 30000; // matching produces up to $30k impact
  const topGoal = 200000;   // highest milestone

  const matchedPortion = Math.min(rawAmount, matchCap);
  const effectiveAmount = rawAmount + matchedPortion; // what the gauge shows

  const milestones = [
    {
      id: 'equipment',
      label: 'allows us to retain and store all of our specialised circus equipment.',
      start: 70000
    },
    {
      id: 'roving',
      label: 'enables us to add a roving programme to Term 2, helping our Youth Circus continue on in their training.',
      start: 80000
    },
    {
      id: 'shared-space',
      label: 'supports our transition to a new, likely shared, space, and the relevant start-up costs associated with re-starting a significant portion of our current class offerings.',
      start: 100000
    },
    {
      id: 'standalone',
      label: 'affords us the possibility of transitioning to a standalone space.',
      start: 120000,
      end: 150000   // band from 120k–150k
    },
    {
      id: 'current-space',
      label: 'allows us to consider the possibility of staying in our current space.',
      start: 200000
    }
  ];

  function formatNZD(amount) {
    return '$' + Math.round(amount).toLocaleString('en-NZ');
  }

  function milestoneLabelAmount(m) {
    if (m.end && m.end !== m.start) {
      return formatNZD(m.start) + '–' + formatNZD(m.end);
    }
    return formatNZD(m.start);
  }

  function milestoneStatus(effective, m) {
    const start = m.start;
    const end = m.end || m.start;

    if (effective >= end) return 'reached';
    if (effective >= start && effective < end) return 'active';
    return 'upcoming';
  }

  // ----- BUILD INNER HTML -----
  container.innerHTML = `
    <div class="circus-fundraising-header">
      <div class="circus-fundraising-title">Support the next chapter of circus in Wellington</div>
      <div class="circus-fundraising-subtitle">Every contribution helps us move through key milestones.</div>
      <div class="circus-match-pill">
        <span class="circus-match-pill-tag">Matched</span>
        <span>The first ${formatNZD(matchCap)} in donations is doubled up to ${formatNZD(matchMaxImpact)} impact.</span>
      </div>
    </div>

    <div class="circus-arc-wrapper">
      <svg class="circus-arc" viewBox="0 0 200 120" aria-hidden="true">
        <!-- semi-circle from left to right along the bottom -->
        <path class="circus-arc-bg" d="M20 100 A80 80 0 0 1 180 100" />
        <path class="circus-arc-impact" d="M20 100 A80 80 0 0 1 180 100" />
        <path class="circus-arc-raw" d="M20 100 A80 80 0 0 1 180 100" />
        <g class="circus-arc-markers"></g>
      </svg>
      <div class="circus-arc-centre">
        <div class="circus-amount-main">${formatNZD(rawAmount)} raised</div>
        <div class="circus-amount-impact">
          Impact including match: <strong>${formatNZD(effectiveAmount)}</strong>
        </div>
        <div class="circus-amount-goal">
          Towards ${formatNZD(topGoal)} and beyond
        </div>
      </div>
    </div>

    <ul class="circus-milestones">
      ${milestones.map(m => {
        const status = milestoneStatus(effectiveAmount, m);
        return `
          <li class="circus-milestone ${status}">
            <span class="circus-milestone-dot"></span>
            <div class="circus-milestone-main">${milestoneLabelAmount(m)}</div>
            <div class="circus-milestone-amount">
              ${status === 'reached' ? 'Reached' : status === 'active' ? 'In progress' : 'Upcoming'}
            </div>
            <div class="circus-milestone-text">${m.label}</div>
          </li>
        `;
      }).join('')}
    </ul>
  `;

  // ----- ARC ANIMATION + MARKERS -----
  const svg = container.querySelector('.circus-arc');
  const impactPath = container.querySelector('.circus-arc-impact');
  const rawPath = container.querySelector('.circus-arc-raw');
  const markersGroup = container.querySelector('.circus-arc-markers');

  const NS = 'http://www.w3.org/2000/svg';
  const centreX = 100;
  const centreY = 100;
  const radius = 80;

  function setArcProgress(path, percent) {
    const length = path.getTotalLength();
    path.style.strokeDasharray = String(length);
    path.style.strokeDashoffset = String(length * (1 - Math.min(Math.max(percent, 0), 1)));
  }

  // position small dots along the arc for each milestone
  milestones.forEach(m => {
    const t = (m.start || 0) / topGoal; // 0–1
    const theta = Math.PI * (1 - Math.min(Math.max(t, 0), 1)); // 180° (left) to 0° (right)
    const x = centreX + radius * Math.cos(theta);
    const y = centreY + radius * Math.sin(theta);

    const circle = document.createElementNS(NS, 'circle');
    circle.setAttribute('cx', x.toFixed(2));
    circle.setAttribute('cy', y.toFixed(2));
    circle.setAttribute('r', '3.3');

    const status = milestoneStatus(effectiveAmount, m);
    circle.classList.add(
      status === 'reached'
        ? 'circus-arc-marker-reached'
        : status === 'active'
        ? 'circus-arc-marker-active'
        : 'circus-arc-marker-upcoming'
    );

    markersGroup.appendChild(circle);
  });

  // apply progress (impact and raw)
  const impactPercent = effectiveAmount / topGoal;
  const rawPercent = rawAmount / topGoal;

  // small timeout so dash animations are visible
  setTimeout(() => {
    setArcProgress(impactPath, impactPercent);
    setArcProgress(rawPath, rawPercent);
  }, 50);
})();
</script>
